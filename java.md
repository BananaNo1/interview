### Java语言有哪些特点

1. 面向对象

2. 平台无关 "一次编写，到处运行"

### 面向对象和面向过程的区别

1. 面向对象方法直接把所有事物都当作独立的对象，处理问题过程中所思考的不再主要是怎样用数据结构来描述问题，而是直接考虑重现问题中各个对象之间的关系。
2. 面向过程是一种以过程为中心的编程思想，它首先分析出解决问题所需要的步骤，然后用函数把这些步骤一步一步实现，在使用时依次调用，是一种基础的顺序的思维方式。

### 基本数据类型

1. byte,short,int,long
2. float,double
3. char
4. boolean

### instanceof 关键字的作用

1. 测试它左边的对象是否是它右边的类的实例，返回 boolean 的数据类型。

### Java自动装箱与拆箱

==自动装箱==就是Java自动将原始类型值转换成对应的对象，比如将int的变量转换成Integer对象，这个过程叫做装箱，反之将Integer对象转换成int类型值，这个过程叫做拆箱。

```java
            int i = 40;  
            int i0 = 40;  
            Integer i1 = 40;  
            Integer i2 = 40;  
            Integer i3 = 0;  
            Integer i4 = new Integer(40);  
            Integer i5 = new Integer(40);  
            Integer i6 = new Integer(0);  
            Double d1=1.0;  
            Double d2=1.0;  

            System.out.println("i=i0\t" + (i == i0));   \\ true   
            System.out.println("i1=i2\t" + (i1 == i2));  \\ true   指向同一个地址 	-128<x<127 拆箱
            System.out.println("i1=i2+i3\t" + (i1 == i2 + i3));  \\ true 数字加减
            System.out.println("i4=i5\t" + (i4 == i5));   \\ false  比较对象内存地址
            System.out.println("i4=i5+i6\t" + (i4 == i5 + i6));  \\ true 数字加减
            System.out.println("d1=d2\t" + (d1==d2));  \\ Double没有缓存 每次都是新对象
```

### 重载和重写的区别

#### 重载(Overload)

在一个类里面，方法名字相同，而参数不同。返回类型可以相同也可以不同。典型就是构造函数的重载

#### 重写(Override)

子类对父类的允许访问的方法的实现过程进行重新编写, 返回值和形参都不能改变。**即外壳不变，核心重写！**

| 区别点   | 重载方法 | 重写方法                                       |
| -------- | -------- | ---------------------------------------------- |
| 参数列表 | 必须修改 | 一定不能修改                                   |
| 返回类型 | 可以修改 | 一定不能修改                                   |
| 异常     | 可以修改 | 可以减少或删除，一定不能抛出新的或者更广的异常 |
| 访问     | 可以修改 | 一定不能做更严格的限制（可以降低限制）         |

### equals与==的区别

#### 关于==

基本类型：比较的是==值==是否相同

引用类型: 比较的是==引用==是否相同

#### 关于equals

equals 默认情况下是引用比较，只是很多类重新了 equals 方法，比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。

### Hashcode的作用

1. HashCode的存在主要是为了查找的快捷性，HashCode是用来在散列存储结构中确定对象的存储地址的
2. 如果两个对象equals相等，那么这两个对象的HashCode一定也相同
3. 如果对象的equals方法被重写，那么对象的HashCode方法也尽量重写
4. 如果两个对象的HashCode相同，不代表两个对象就相同,即equals()不一定为true，只能说明这两个对象在散列存储结构中，存放于同一个位置

### String、StringBuﬀer 和StringBuilder 的区别是什么?

```java
都是final类，都不允许被继承
String是长度不可变的，StringBuffer和StringBuilder类长度是可变的
StringBuffer是线程安全的,方法用synchronized修饰
StringBuilder不是线程安全的
```

### ArrayList和LinkedList的区别

1. ArrayList基于数组，LinkedList的实现是基于双向链表。
2. 对于随机访问，ArrayList优于LinkedList
3. 对于插入和删除操作，LinkedList优于ArrayList
4. LinkedList比ArrayList更占内存，因为LinkedList的节点除了存储数据，还存储了两个引用，一个指向前一个元素，一个指向后一个元素。

### Java的四种引用，强弱软虚

1. 强引用
   只要强引用关系还存在，虚拟机就不会回收
2. 软引用
   在系统将要发生内存溢出前，会把这些对象列进回收范围之内进行二次回收。如果内存空间不足，垃圾回收器就会进行回收
3. 弱引用
   只能生存到下一次垃圾收集发生为止。一旦发现了只有弱引用的对象，垃圾回收器就会进行回收。
4. 虚引用
   目的只是为了能在这个对象被收集器回收时收到一个系统通知。 如果发现该对象还具有虚引用，就会在回收该对象之前，吧这个虚引用加入到与之关联的引用队列中。

### 泛型常用特点

编写的代码可以被不同类型的对象所重用。#todo

### Java创建对象有几种方式？

1. 使用`new`关键字。需要使用构造器。

2. 反射机制。用Class类或者Constructor的newInstance()方法。需要使用构造器。

3. 通过object类的clone方法。不会调用构造器。
4. 使用反序列化。当我们序列化和反序列化一个对象，jvm会给我们创建一个单独的对象。在反序列化时，JVM创建对象并不会调用任何构造函数。

### 深拷贝和浅拷贝的区别是什么?

1. 深拷贝 创建一个新的对象和数组，将原对象的各项属性的“值”(数组的所有元素)拷贝过来，是"值"
2. 浅拷贝 将原对象或数组的引用直接赋给新对象、新数组，新对象、新数组只是原对象的==一个引用==

### ﬁnal有哪些用法?

1. 用来修饰数据，包括成员变量和局部变量，该变量只能被赋值一次且它的值无法被改变。对于成员变量而言，必须在声明或者构造方法中对它赋值。
2. 用来修饰方法参数，表示在变量的生存期中它的值不能被改变。
3. 修饰方法，表示该方法无法被重写。
4. 修饰类，表示该类无法被继承。

### static都有哪些用法?

1. 修饰成员变量，将其变为类的成员，从而实现所有对象对于该成员的共享。
2. 修饰成员方法，将其变为类方法，可以直接使用"类名.方法名"的方式调用，常用于工具类。
3. 静态块用法，将多个类成员放在一起初始化，使得程序更加规整，其中理解对象的初始化过程非常关键；
4. 静态导包用法，将类的方法直接导入到当前类中，从而直接使用**“方法名”**即可调用类方法，更加方便。

### 3*0.1 == 0.3返回值是什么

```java
false  //有些浮点数不能完全精确的表示出来
    
System.out.println(3 * 0.1); // 0.30000000000000004    
```

### a=a+b与a+=b有什么区别吗?

```
有
在两个变量的数据类型一样时：a+=b 和a=a+b 是没有区别的。
当两个变量的数据类型不同时，就需要考虑一下数据类型自动转换的问题了。'+='会自动进行类型转换

整数运算规则：
	如果两个操作数有一个为long，则结果也为long；
	没有long时，结果为int。即使操作数全为short、byte，结果也是int。
```

### try catch ﬁnally，try里有return，ﬁnally还执行么？

会执行。

1. try中有return, 会先将值暂存，无论finally语句中对该值做什么处理，最终返回的都是try语句中的暂存值。
2. 当try与finally语句中均有return语句，会忽略try中return。

### 线程有哪些基本状态?

- new: 新线程，尚未执行。
- Runnable：运行中的线程，正在执行`run()`方法的java代码。
- Blocked: 运行中的线程，因为某些操作被阻塞而挂起。
- Waiting：运行中的线程，因为某些操作在等待中。
- Time Waiting: 运行中的线程，因为执行`sleep()`方法正在计时等待。
- Terminaled:线程已终止，`run()`执行完毕。

### Java 序列化中如果有些字段不想进行序列化，怎么办？

对于不想进行序列化的变量，使用==transient== 关键字修饰。transient 关键字的作用是：阻止实例中那些用此关键字修饰的的变量序列化；当对象被反序列化时，被transient 修饰的变量值不会被持久化和恢复。

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

