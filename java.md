### 容器

#### List
1. 可以看出向 ArrayList 添加大量元素之前最好先使用ensureCapacity 方法，以减少增量重新分配的次数。

#### 队列
PriorityQueue：元素出队顺序是与优先级相关的，即总是优先级最高的元素先出队。
1. 利用了二叉堆的数据结构来实现 
2. 是非线程安全的
3. 典型例题包括堆排序、求第K大的数、带权图的遍历等，需要会熟练使用。

#### Map

##### HashMap
1. 非线程安全
2. 可以存储 null 的 key 和 value，但 null 作为键只能有一个，null 作为值可以有多个
3. HashMap 默认的初始化大小为 16。之后每次扩充，容量变为原来的 2 倍。
4. 当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。
    1. 红黑树就是为了解决二叉查找树的缺陷，因为二叉查找树在某些情况下会退化成一个线性结构。


##### HashMap 源码
1. loadFactor  控制数组存放数据的疏密程度，loadFactor 越趋近于 1，那么 数组中存放的数据(entry)也就越多，也就越密。loadFactor 的默认值为 0.75f 
2. threshold = capacity * loadFactor 衡量数组是否需要扩增的一个标准

#####  ConcurrentHashMap

##### 1.7
1. 结构 `Segment 数组 + HashEntry 数组 + 链表`  并发直接锁`Segment` 使用`ReentrantLock` Segment 的个数一旦初始化就不能改变，默认 Segment 的个数是 16 个，可以认为 ConcurrentHashMap 默认支持最多 16 个线程并发。

##### 1.8
1. 结构 `Node 数组 + 链表 / 红黑树`
2. 并发锁`node` 也就是每个数组位置一个锁  ` Synchronized锁 自旋和 CAS`

linux 查看进程 `ps -ef`

jconsole远程连接java进程  
在linux上启动java进程 `java -Djava.rmi.server.hostname=192.168.1.121 -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.pore=12345 -Dcom.sun.management.jmxremote.ssl=false -Dcom.management.jmxremote.authenticate=false name`


