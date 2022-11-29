### Java语言有哪些特点

1. 面向对象
2. 平台无关 "一次编写，到处运行"

### 面向对象的三大特性

1. 封装
2. 继承
3. 多态 同一个行为有不同的表现形式或形态的能力

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

#### 	好处

- 类型安全  在编译期间可以检测类型错误

- 消除了代码中的强制类型转换

  #### 原理

  ​	**类型擦除**    在生成的 Java 字节代码中是不包含泛型中的类型信息的。使用泛型的时候加上的类型参数，会被编译器在编译的时候去掉。这个过程就称为类型擦除。如在代码中定义的`List<Object>`和`List<String>`等类型，在编译之后都会变成List。

### Java创建对象有几种方式？

1. 使用`new`关键字。需要使用构造器。

2. 反射机制。用Class类或者Constructor的newInstance()方法。需要使用构造器。

3. 通过object类的clone方法。不会调用构造器。
4. 使用反序列化。当我们序列化和反序列化一个对象，jvm会给我们创建一个单独的对象。在反序列化时，JVM创建对象并不会调用任何构造函数。

### 接口和抽象类的区别

1. 实现接口的关键字是`implements` 继承抽象类的关键字是`extends`。一个类可以实现多个接口，但一个类只能继承一个抽象类。
2. 接口只有定义，不能有方法的实现，java 1.8中可以定义default方法体，而抽象类可以有定义与实现，方法可在抽象类中实现。

### 为什么要设计接口内可以有方法实现?

在 Java 8 之前，比如要在一个接口中添加一个抽象方法，那所有的接口实现类都要去实现这个方法，不然就会编译错误，而某些实现类根本就不需要实现这个方法也被迫要写一个空实现，改动会非常大。

所以，接口默认方法就是为了解决这个问题，只要在一个接口添加了一个默认方法，所有的实现类就自动继承，不需要改动任何实现类，也不会影响业务，爽歪歪。

另外，接口默认方法可以被接口实现类重写。

### 抽象类不能实例化为什么有构造方法

1. java规定类都要有一个构造方法，没有默认提供一个空参构造
2. 抽象方法需要被子类继承，但实例化子类的时候，就会初始化父类，不管父类是不是抽象类都会调用父类的构造方法
3. 构造方法不是用来实例化的，而是用来给属性初始化赋值的，抽象方法可以定义属性，那么就需要构造方法给属性赋值。

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

### 继承Thread类和实现runnable接口有什么区别

**继承 Thread 类：**我们创建了三次 MyThread 对象，也就是创建了三个任务（工作四个小时这个任务）和三个线程（张三，李四和赵五三个人），每个线程去完成自己的任务（每个人去完成自己的工作）；**即多个线程分别完成自己的任务。**

**实现 Runnable 接口：**我们创建了一个 MyRunnabld 对象（工作八个小时这个任务）和三个线程（张三，李四和赵五三个人）；让这三个线程去共同完成一个任务（三个人共同完成一个工作）；**即多个线程共同完成一个任务。**

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

​	基于数组+链表

1. 非线程安全
2. 可以存储 null 的 key 和 value，但 null 作为键只能有一个，null 作为值可以有多个
3. HashMap 默认的初始化大小为 16。扩容因子为0.75 ,之后每次扩充，容量变为原来的 2 倍。每当我们数组中的存储容量达到 75%的时候，就需要对数组容量进行 2 倍的扩容。
4. 当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。
    1. 红黑树就是为了解决二叉查找树的缺陷，因为二叉查找树在某些情况下会退化成一个线性 结构。

5. JDK1.7 时是在表头节点插入的，会出现**链表成环**问题 ,JDK1.8 之后是在尾节点插入的。


##### HashMap 源码
1. loadFactor  控制数组存放数据的疏密程度，loadFactor 越趋近于 1，那么 数组中存放的数据(entry)也就越多，也就越密。loadFactor 的默认值为 0.75f 
2. threshold = capacity * loadFactor 衡量数组是否需要扩增的一个标准

#####  ConcurrentHashMap

##### 1.7
1. 结构 `Segment 数组 + HashEntry 数组 + 链表`  并发直接锁`Segment` 使用`ReentrantLock` Segment 的个数一旦初始化就不能改变，默认 Segment 的个数是 16 个，可以认为 ConcurrentHashMap 默认支持最多 16 个线程并发。
1. 分段锁 实现线程安全

##### 1.8
1. 结构 `Node 数组 + 链表 / 红黑树`
2. 并发锁`node` 也就是每个数组位置一个锁  ` Synchronized锁 自旋和 CAS`

linux 查看进程 `ps -ef`

jconsole远程连接java进程  
在linux上启动java进程 `java -Djava.rmi.server.hostname=192.168.1.121 -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.pore=12345 -Dcom.sun.management.jmxremote.ssl=false -Dcom.management.jmxremote.authenticate=false name`

### Synchronize的原理

​	**Synchronized的语义底层是通过一个monitor的对象来完成，其实wait/notify等方法也依赖于monitor对象，这就是为什么只有在同步的块或者方法中才能调用wait/notify等方法，否则会抛出java.lang.IllegalMonitorStateException的异常的原因。**

### Synchronize优化

#### 	1. 锁膨胀

#### 	2. 锁消除

​			`JIT`编译器（`Just In Time`编译器）可以在动态编译同步代码时，使用一种叫做**逃逸分析**的技术，来通过该项技术判别程序中所使用的锁对象是否只被一个线程所使用，而没有散布到其他线程当中；如果情况就是这样的话，那么JIT编译器在编译这个同步代码时就不会生成synchronized关键字标识的锁的申请和释放机器码，从而消除了锁的使用流程。

#### 	3. 锁粗化

### **Synchronized**锁的升级过程

锁的四种状态: 无锁->偏向锁->轻量级锁->重量锁

1. 无锁没有对资源进行锁定，所有的线程都能访问并修改同一个资源，但同时只有一个线程能修改成功。
2. 偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁，降低获取锁的代价。

3. 当锁是偏向锁的时候，被另外的线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，从而提高性能。
4. 当自旋超过一定的次数，或者一个线程在持有锁，一个在自旋，又有第三个来访时，轻量级锁升级为重量级锁。

### **Synchronized**锁和ReentrantLock区别

1. `synchronized`是java内置的关键字，`ReentrantLock`是java的一个类
2. `synchronized`只能是非公平锁，`ReentrantLock`可以实现公平和非公平锁
3. `synchronized`不能中断一个等待锁的线程，而lock可以中断一个试图获取锁的线程
4. `synchronized`不能超时，`ReentrantLock`可以超时
5. `synchronized`会自动释放锁，`ReentrantLock`必须手动释放

### **wait()方法为什么要放在Object类中？**

1. **每个对象都可上锁**，这是在 Object 类而不是 Thread 类中声明 wait 和 notify 的另一个原因。

### 创建多线程的方法

	1. 继承Thread类，重写run()方法
	2. 实现Runnable接口，重写run()
	3. 带返回值的线程(实现implements Callable<返回值类型>)
	4. 线程池的实现

```java
1.
    public class Demo extends Thread{
        
        public void run(){
            System.out.println("Thread running");
        }
        
        public static void main(String[] args){
            Demo demo1 =new Demo();
            Demo demo2 =new Demo();
            demo1.start();
            demo2.start();
        }
    }

2.
    public class Demo implements Runnable{
        public void run(){
            System.out.println("implements running");
        }
        
        public static void main(String[] args){
            Thread t1 =new Thread(new Demo());
            Thread t2 =new Thread(new Demo());
            t1.start();
            t2.start();
        }
    }
比第一种继承Thread类的方式，使用了面向接口，将任务与线程进行分离，有利于解耦
3. 
    public class Demo implements Callable<String>{
        
        public String call()  throw Exception {
            Thread.sleep(2000);
            return "睡了2s后返回执行结果";
        }
        
        public static void main(String[] args){
            Demo d =new Demo();
            FutureTask<String> task = new FutureTask<>(d);
            Thread t= new Thread(task);
            t.start();
            System.out.println("提前完成任务...");
            String result = task.get();
            System.out.println("线程执行结果为"+result);
        }
        
    }

4. 
	public class Demo{
        public static void main(String[] args){
            Executor threadPool =new Executors.newFixedThreadPool(5);
            for(int i=0;i<10;i++){
                threadPool.execute(new Runnable(){
                   	public void run(){
                    	System.out.println(Thread.currentThread().getName()+"is running");   
                   	} 
                });
            }
        }
    }
```

### Comparable Comparator区别

1. Comparable是排序接口，若一个类实现了Comparable接口，就意味着“该类支持排序”。而Comparator是比较器，我们若需要控制某个类的次序，可以建立一个“该类的比较器”来进行排序。
2. Comparable相当于“内部比较器”，而Comparator相当于“外部比较器”。Comparable写在类内部，Comparator新建一个类，实现Comparator接口
3. 两种方法各有优劣， 用Comparable 简单， 只要实现Comparable 接口的对象直接就成为一个可以比较的对象，但是需要修改源代码。 用Comparator 的好处是不需要修改源代码， 而是另外实现一个比较器， 当某个自定义的对象需要作比较的时候，把比较器和对象一起传递过去就可以比大小了， 并且在Comparator 里面用户可以自己实现复杂的可以通用的逻辑，使其可以匹配一些比较简单的对象，那样就可以节省很多重复劳动了。

### 阻塞队列

1. 支持阻塞的**插入**方法：队列满时，队列会阻塞插入元素的线程，直到队列不满。
2. 支持阻塞的**移除**方法：队列空时，获取元素的线程会等待队列变为非空。

```
ArrayBlockingQueue：数组结构组成的有界阻塞队列
LinkedBlockingQueue：一个由链表结构组成的有界阻塞队列
PriorityBlockingQueue：支持优先级的无界阻塞队列
DelayQueue：支持延时获取元素的无界阻塞队列，即可以指定多久才能从队列中获取当前元素
SynchronousQueue：不存储元素的阻塞队列，每一个put必须等待一个take操作，否则不能继续添加元素。并且他支持公平访问队列。
```

### 反射中 class.Forname和classloader的区别

​	区别

```
（1）Class.forName除了将类的.class文件加载到jvm中之外，还会对类进行解释，执行类中的static块。
（2）而classloader只干一件事情，就是将.class文件加载到jvm中，不会执行static中的内容，只有在newInstance才会去执行static块。
```

### 字节流和字符流的区别

1. 字节流操作的基本单元为字节；字符流操作的基本单元为`Unicode`码元。
2. 字节流默认不使用缓冲区；字符流使用缓冲区。
3. 字节流在操作的时候本身是不会用到缓冲区的，是与文件本身直接操作的，所以字节流在操作文件时，即使不关闭资源，文件也能输出；字符流在操作的时候是使用到缓冲区的。如果字符流不调用close或flush方法，则不会输出任何内容。
4. 字节流可用于**任何类型的对象**，包括二进制对象，而字符流只能处理**字符或者字符串**；字节流提供了处理任何类型的IO操作的功能，但它不能直接处理Unicode字符，而字符流就可以。

### 双重检测单例模式

```java
    private static volatile Singleton singleton;

    public static Singleton getInstance() {
        if (singleton == null) {
            synchronized (Singleton.class) {
                if (singleton == null) {
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
```

#### 第一个 if

​	如果去掉它，那么所有线程都会串行执行，效率低下

#### 第二个 if

 没有会破坏单例模式

两个线程同时调用 getInstance 方法，由于 singleton 是空的 ，因此两个线程都可以通过第一重的 if 判断；然后由于锁机制的存在，会有一个线程先进入同步语句，并进入第二重 if 判断 ，而另外的一个线程就会在外面等待。

### 为什么要设计接口和抽象类

接口是对动作的抽象，而抽象类是对根源的抽象。

### java错误与异常

#### 错误 error

一般是指java虚拟机相关的问题，如系统崩溃、虚拟机出错误、动态链接失败等，这种错误无法恢复或不可能捕获，将导致应用程序中断，通常应用程序无法处理这些错误

#### 异常

##### 运行时异常 RuntimeException

`NullPointerException` `IndexOutOfBoundsException` 

这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。

##### 非运行时异常 

`IOException` `InterruptedException`(Thread.sleep)

对于这种异常，JAVA编译器**强制**要求我们**必需**对出现的这些异常进行catch并**处理**，否则程序就不能编译通过。

### Collection和Collections的区别

1. Collection 是一个集合接口，提供了对集合进行基本操作的通用接口
   1. size()
   2. isEmpty()
   3. contains
2. 包装类，包含各种有关集合操作的静态方法。
