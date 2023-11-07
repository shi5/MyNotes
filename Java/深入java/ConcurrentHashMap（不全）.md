- HashMap存在线程安全问题，有多个解决方法，比如改用 HashTable 或者 Collections.synchronizedMap() 方法。但这二者都存在性能问题，无论读写，都会给整个集合加锁，导致同一时间的其他操作阻塞。
- ConcurrentHashMap 的优势在于兼顾性能和线程安全，一个线程进行写操作时，它会锁住一小部分，其他部分的读写不受影响，其他线程访问没上锁的地方不会被阻塞。

## 实现原理

### JDK1.7

- ConcurrentHashMap 由 `Segment` 数组结构和 `HashEntry` 数组结构组成
- ConcurrentHashMap 把哈希桶数组切分成`Segment` ，每个`Segment`有 n 个 `HashEntry` 组成。

如图所示：![[Pasted image 20231031150037.png]]

### JDK1.8

- 数据结构：Node数组+链表+红黑树
- 锁实现：CAS+synchronized

如图所示：![[Pasted image 20231031154233.png]]