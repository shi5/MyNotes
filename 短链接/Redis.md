Redis底层采用C语言编写

## Redis为什么这么快

1. Redis是基于内存操作的
2. Redis基于Reactor模式设计开发了一套高效的事务处理模型——单线程事件循环和IO多路复用
3. Redis对内置的数据结构进行了优化，性能很高
4. Redis通信协议实现简单且通信高效

## Redis类型

### 基础类型

5种：String、List、Set、Hash、Zset(有序集合)

String:

