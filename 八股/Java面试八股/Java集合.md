## 概述

由两大接口派生而来：
-  `Collection`：`List`、`Set` 和 `Queue`
-  `Map`

### 重要类

- `HashMap`：JDK1.8 之前 `HashMap` 由数组+链表组成的，数组是 `HashMap` 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突）。JDK1.8 以后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。

- `LinkedList`性能不好，实现栈和双向队列建议使用`ArrayDeque`

- `HashSet` 用于不需要保证元素插入和取出顺序的场景，`LinkedHashSet` 用于保证元素的插入和取出顺序满足 FIFO 的场景，`TreeSet` 用于支持对元素自定义排序规则的场景。

## Set

### Comparable 和 Comparator 的区别

- `Comparable` 接口实际上是出自`java.lang`包 它有一个 `compareTo(Object obj)`方法用来排序
- `Comparator`接口实际上是出自 `java.util` 包它有一个`compare(Object obj1, Object obj2)`方法用来排序

需要对一个集合使用自定义排序时，要重写`compareTo()`方法或`compare()`方法

## Queue

**因为容量问题而导致操作失败后处理方式的不同** 可以分为两类方法: 一种在操作失败后会抛出异常，另一种则会返回特殊值。
![[Pasted image 20240324215717.png]]


### ArrayDeque 与 LinkedList 的区别

- `ArrayDeque` 是基于可变长的数组和双指针来实现，而 `LinkedList` 则通过链表来实现。
- `ArrayDeque` 不支持存储 `NULL` 数据，但 `LinkedList` 支持。
- `ArrayDeque` 是在 JDK1.6 才被引入的，而`LinkedList` 早在 JDK1.2 时就已经存在。
- `ArrayDeque` 插入时可能存在扩容过程, 不过均摊后的插入操作依然为 O(1)。虽然 `LinkedList` 不需要扩容，但是每次插入数据时均需要申请新的堆空间，均摊性能相比更慢。

> 从性能的角度上，选用 `ArrayDeque` 来实现队列、栈要比 `LinkedList` 更好

### PriorityQueue

 - 利用了二叉堆的数据结构来实现的，底层使用可变长的数组来存储数据
 - 通过堆元素的上浮和下沉，实现了在 O(logn) 的时间复杂度内插入元素和删除堆顶元素。
 - 是非线程安全的，且不支持存储 `NULL` 和 `non-comparable` 的对象。

### BlockingQueue

`BlockingQueue` （阻塞队列）是一个接口，继承自 `Queue`。`BlockingQueue`阻塞的原因是其支持当队列没有元素时一直阻塞，直到有元素；还支持如果队列已满，一直等到队列可以放入新元素时再放入。

`BlockingQueue` 常用于生产者-消费者模型中，生产者线程会向队列中添加数据，而消费者线程会从队列中取出数据进行处理。

## Map #重要

### HashMap 和 Hashtable 的区别

- **线程是否安全：** `HashMap` 是非线程安全的，`Hashtable` 是线程安全的,因为 `Hashtable` 内部的方法基本都经过`synchronized` 修饰。
- **效率：** 因为线程安全的问题，`HashMap` 要比 `Hashtable` 效率高一点。另外，`Hashtable` 基本被淘汰，不要在代码中使用它；
- **对 Null key 和 Null value 的支持：**`HashMap` 可以存储 null 的 key 和 value，Hashtable 不允许有 null 键和 null 值，否则会抛出 `NullPointerException`
- **初始容量大小和每次扩充容量大小的不同：**`Hashtable` 默认的初始大小为 11，之后每次扩充，容量变为原来的 2n+1。     `HashMap` 默认的初始化大小为 16。之后每次扩充，容量变为原来的 2 倍。    创建时如果给定了容量初始值，那么 `Hashtable` 会直接使用你给定的大小，而 `HashMap` 会将其扩充为 2 的幂次方大小（`HashMap` 中的`tableSizeFor()`方法保证）
- **底层数据结构**不同

### HashSet 如何检查重复

`HashSet` 底层就是基于 `HashMap` 实现的。

在 JDK1.8 中，`HashSet`的`add()`方法只是简单的调用了`HashMap`的`put()`方法，并且判断了一下返回值以确保是否有重复元素。

也就是说，在 JDK1.8 中，实际上无论`HashSet`中是否已经存在了某元素，`HashSet`都会直接插入，只是会在`add()`方法的返回值处告诉我们插入前是否存在相同元素。

### HashMap底层实现

HashMap 通过 key 的 `hashcode` 经过**扰动函数**处理过后得到 hash 值，然后通过 `(n - 1) & hash` 判断当前元素存放的位置（这里的 n 指的是数组的长度），如果当前位置存在元素的话，就判断该元素与要存入的元素的 hash 值以及 key 是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突。

所谓扰动函数指的就是 HashMap 的 `hash` 方法。使用 `hash` 方法也就是扰动函数是为了防止一些实现比较差的 `hashCode()` 方法 换句话说使用扰动函数之后可以减少碰撞。

![[Pasted image 20240326101215.png]]

### HashMap 的长度为什么是 2 的幂次方

Hash的范围值-2147483648 到 2147483647，前后加起来大概 40 亿的映射空间，但是40 亿长度的数组，内存是放不下的。所以这个散列值是不能直接拿来用的。用之前还要先做对数组的长度取模运算，得到的余数才能用来要存放的位置也就是对应的数组下标。这个数组下标的计算方法是“ `(n - 1) & hash`”。（n 代表数组长度）。因此数组的长度应该按照位数计算（2的幂次方）

### HashMap 多线程操作导致死循环问题（JDK1.7）

JDK1.7 及之前版本的 `HashMap` 在多线程环境下扩容操作可能存在死循环问题，这是由于当一个桶位中有多个元素需要进行扩容时，多个线程同时对链表进行操作，头插法可能会导致链表中的节点指向错误的位置，从而形成一个环形链表，进而使得查询元素的操作陷入死循环无法结束。

JDK1.8版本采用尾插法来解决这个问题，但多线程下使用 `HashMap` 还是会存在数据覆盖的问题。

### HashMap为什么线程不安全

两种情况：
1. 两个线程 1,2 同时进行 put 操作，并且发生了哈希冲突
2. 两个线程同时 `put` 操作导致 `size` 的值不正确，进而导致数据覆盖的问题

### HashMap的遍历方式

1. 使用迭代器（Iterator）EntrySet 的方式进行遍历；
2. 使用迭代器（Iterator）KeySet 的方式进行遍历；
3. 使用 For Each EntrySet 的方式进行遍历；
4. 使用 For Each KeySet 的方式进行遍历；
5. 使用 Lambda 表达式的方式进行遍历；
6. 使用 Streams API 单线程的方式进行遍历；
7. 使用 Streams API 多线程的方式进行遍历。

`entrySet` 的性能比 `keySet` 的性能高出了一倍之多，因此我们应该尽量使用 `entrySet`  来实现 Map 集合的遍历。
应该尽量使用迭代器（Iterator）来遍历 `EntrySet` 的遍历方式来操作 Map 集合

### ConcurrentHashMap 和 Hashtable 的区别

`ConcurrentHashMap` 和 `Hashtable` 的区别主要体现在实现线程安全的方式上不同。

-  JDK1.7 的 `ConcurrentHashMap` 底层采用 **分段的数组+链表** 实现，JDK1.8 采用的数据结构跟 `HashMap1.8` 的结构一样，数组+链表/红黑二叉树。`Hashtable` 和 JDK1.8 之前的 `HashMap` 的底层数据结构类似都是采用 **数组+链表** 的形式，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的；
- 实现线程安全的方式 #重要 
	- 