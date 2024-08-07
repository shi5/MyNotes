## 消息持久化

- 为什么要采取文件系统存储，而不是数据库
	- 效率上，本地磁盘更快，比数据库少了网络开销（主要）
	- 数据稳定性更高
	- 减少了第三方依赖

- 为什么要采用commitlog + consumerqueue的方式 
	- 可以实现顺序写入与近似顺序读取，**磁盘的顺序IO**（不是说文件是完全连续的）效率很高，磁头几乎不用换道，或者换道的时间很短。（*磁盘与内存相关知识需要补充*）
	- 对于commitlog，所有消息写入都是顺序的，消息读取是随机的，但整体上还是顺序读的，因为是**都是从旧数据到新数据有序的读取。**
	- 由于Consume Queue存储数据量极少，而且是顺序读，在PAGECACHE预读作用下，Consume Queue的读性能几乎与内存一致，即使堆积情况下。所以可认为Consume Queue完全不会阻碍读性能
> 根据之前阿里中间件团队的测试，一旦kafka中Topic的partitoin数量过多，队列文件会过多，会给磁盘的IO读写造成很大的压力，造成tps迅速下降。

- 页缓存的不确定性和mmap的惰性加载（实际使用才真正加载）如何解决
	- **文件预分配**和**文件预热**。
		- 文件预分配：
		- 文件预热：

- 针对mmap的优化
	- 文件预分配：创建下一个MappedFile时，会将下下个创建好（将请求放入在AllocateMappedFileService服务线程）
	- mlock：其可以将进程使用的部分或者全部的地址空间锁定在物理内存中，防止其被交换到swap空间。
	- madvise： mmap映射后不会将文件立马加载到内存，而是使用时加载，RocketMQ的做法是，在做Mmap内存映射的同时进行madvise系统调用，目的是使OS做一次内存映射后对应的文件数据尽可能多的预加载至内存中，从而达到内存预热的效果。
	- 使用引用数来管理MappedByteBuffer的释放，MappedByteBuffer的回收是由JVM来决定的，回收时间是无法保证的。

> FileChannel在调用了map方法，进行内存映射得到MappedByteBuffer，但是没有提供unmap方法()，释放内存。事实上，unmap方法是在FileChannelImpl类里实现的，是个私有方法。在finalize延迟的时候，unmap方法无法调用，在删除文件的时候就会因为内存未释放而失败。不过可以通过显示的调用unmap方法来释放内存(本质是调用**sun.misc.Cleaner的clean**方法）。


- 为什么要采用mmap而不是sendfile，而Kafka是采用sendfile（*可以继续深入研究*
	- 零拷贝包括两种方式，RocketMQ 使用**mmap+write**，因单个消息是小块数据，小块数据传输的要求效果比 sendfile 方式好
		- mmap支持NIO，sendfile只能BIO传输，那么NIO的特性本身就会对数据块小、请求个数多的传输需求有很好的支持。
	- RocketMQ一个CommitLog会有多个Topic的消息，服务端需要做查找过滤，也就是需要对磁盘文件读取出来的结果做加工再返回，而Kafka一个segment文件是partition级别的，不需要处理直接返回给客户端就行，所以用sendfile会更合适
![[QQ_1720688553261.png]]

- 如何释放MappedByteBuffer?
	- ![[QQ_1721268686007.png]]
	- RocketMQ中采用**第二种方法**，嵌套递归获取directByteBuffer的最内部的attachment或者viewedBuffer方法获取directByteBuffer的Cleaner对象，然后调用cleaner.clean方法，进行释放资源

- 异步刷盘可使用transientStorePool
	- transientStorePool使用堆外内存（直接内存），速度更快，RokcetMQ引入该机制是为了提供一种内存锁定，将当前堆外内存一直锁定在内存中，避免被进程将内存交换到磁盘中
	- mappedByteBuffer使用pagecache，写入速度受限于操作系统管理和调度
	- 有了 TransientStorePool 的存在，消息可以批量写入内存缓冲区，RocketMQ 也就可以有效地控制何时以及如何将脏页（Dirty Page，即已修改但还未写入磁盘的内存页）刷写到磁盘，**避免了操作系统自动进行的随机性、不可预测的脏页刷写操作**，从而提升了**I/O**性能，特别是在大量写入请求的场景下。
	-  使用transientStorePool如果发生**服务器断电、服务宕机**都会产生消息丢失，而普通的异步刷盘，由于消息是直接写入内存映射区域，所以服务宕机并不会丢失数据，**只有在服务器突然断电时**才会丢失少量数据。

## 获取消息方式

Consumer 端的两种消费模式（Push/Pull）都是基于拉模式来获取消息的，而在 Push 模式只是对 pull 模式的一种封装，其**本质实现为消息拉取线程在从服务器拉取到一批消息后，然后提交到消息消费线程池后，又“马不停蹄”的继续向服务器再次尝试拉取消息**。如果未拉取到消息，则延迟一下又继续拉取。

因此，有必要在 Consumer 端来做负载均衡

消息消费队列在同一消费组不同消费者之间的负载均衡，其核心设计理念是**在一个消息消费队列在同一时间只允许被同一消费组内的一个消费者消费，一个消息消费者能同时消费多个消息队列。**

## 消息过滤

### tag过滤

二层过滤：1. 在Broker中使用ConsumeQueue中的HashCode做粗粒度过滤；2. 消费端拉取消息后做精细过滤

- 为什么不在Broker做精细过滤：根据消息属性精确匹配的性能消耗比较大，所以先根据消费队列进行一次过滤，剩下的消息再根据消息详情过滤，可以减少比较次数，提升Broker性能。

## 消息发送

RocketMQ消息队列中支持通信的方式主要有同步(sync)、异步(async)、单向(oneway) 三种

## 重复消费

![[Pasted image 20240726161547.png]]

## 事务消息

