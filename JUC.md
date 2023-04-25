### 三程

1. 进程  在系统中运行的一个应用程序就是进程，每一个进程都有自己的内存空间和系统资源
2. 线程  在同一个进程内会有1个或多个线程
3. **管程**  Monitor 平时所说的锁 每个对象实例都会有一个Monitor对象

​	

runnable 和 callable 接口区别   是否有返回值 是否抛出异常



futuretask 对于结果的获得不是很友好  

  	.get()  会导致阻塞    .isDone()  导致cpu轮询检查 ，浪费资源

### completableFuture  

completableFuture  实现 future 和completableStage 接口

**传入回调对象，当异步任务完成或者发生异常时，自动调用回调对象的回调方法。**

completableFuture  使用4个静态方法 创建 

1. runAsync  无返回值
2. supplyAsync  有返回值 

不传入线程池参数 默认使用 ForkJoinPool.commonPool





join和get区别

   get编译时抛出异常   join不抛出异常





| 函数式接口名称 | 方法名称 | 参数 | 返回值 |
| -------------- | -------- | ---- | ------ |
| Runnable       | run      | 无   | 无     |
| Function       | apply    | 1    | 有     |
| Consume        | accept   | 1    | 无     |
| Supplier       | get      | 无   | 有     |
| BioConsumer    | accept   | 2    | 无     |



调用thenRun方法执行第二个任务时，则第二个任务和第一个任务是共同使用一个线程池

调用thenRunAsync执行第二个任务时，则第一个任务使用的是你自己传入的线程池，第二个任务使用的是ForkJoin线程池

### Synchronized

#### 作用在三个地方

1. 作用于实例方法，当前实例加锁，进入同步代码前要获得当前实例的锁
2. 作用域代码块，对括号里配置的对象加锁
3. 作用域静态方法，当前类加锁，进去同步代码前摇获得当前类对象的锁

#### Synchronized 作用域同步块

​	实现使用的是 `monitorenter` 和 `monitorexit`

​	一般情况就是1个enter对应2个exit   对应一个正常退出  一个异常退出

```java
public class LockSyncDemo {
    Object object = new Object();
    public void m1() {
        synchronized (this) {
            System.out.println("hello***");
        }
    }
    public static void main(String[] args) {

    }
}
```

```
 javap  -c LockSyncDemo.class
 
 public void m1();
    Code:
       0: aload_0
       1: dup
       2: astore_1
       3: monitorenter       
       4: getstatic     #4                  // Field java/lang/System.out:Ljava/io/PrintStream;
       7: ldc           #5                  // String hello***
       9: invokevirtual #6                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      12: aload_1
      13: monitorexit          正常退出
      14: goto          22
      17: astore_2
      18: aload_1
      19: monitorexit           异常退出
      20: aload_2
      21: athrow              异常
      22: return
```

#### Synchronized  普通同步方法

```java
    public synchronized  void m2(){
        System.out.println("hello  m2");
    }
```

```
javap -v  LockSyncDemo.class


  public synchronized void m2();
    descriptor: ()V
    flags: ACC_PUBLIC, ACC_SYNCHRONIZED   同步标识
    Code:
      stack=2, locals=1, args_size=1
         0: getstatic     #4                  // Field java/lang/System.out:Ljava/io/PrintStream;
         3: ldc           #7                  // String hello  m2
         5: invokevirtual #6                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
         8: return
      LineNumberTable:
        line 12: 0
        line 13: 8
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       9     0  this   LLockSyncDemo;
```

####   Synchronized  静态同步方法

```
    public static synchronized void m3() {
        System.out.println("hello  m3");
    }
```



```
javap -v  LockSyncDemo.class

  public static synchronized void m3();
    descriptor: ()V
    flags: ACC_PUBLIC, ACC_STATIC, ACC_SYNCHRONIZED   静态 同步标识
    Code:
      stack=2, locals=0, args_size=0
         0: getstatic     #4                  // Field java/lang/System.out:Ljava/io/PrintStream;
         3: ldc           #8                  // String hello  m3
         5: invokevirtual #6                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
         8: return
      LineNumberTable:
        line 16: 0
        line 17: 8
```

#### 为什么任何一个对象都可以成为一个锁

`monitor`采用`ObjectMonitor` 实现

`ObjectMonitor.java`->`ObjectMonitor.cpp`->`ObjectMonitor.hpp`

每个对象天生都带着一个对象监视器

每一个被锁住的对象都会和Monitor对象关联起来

### 公平锁和非公平锁

#### 公平锁

多个线程按照申请锁的顺序来获取锁

#### 非公平锁

多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁，在高并发环境下，有可能造成优先级翻转或者饥饿的状态(某个线程一直获取不到锁)

**非公平锁减少线程切换的开销**

### 可重入锁

**每个锁对象拥有一个锁计数器和一个指向持有该锁的线程的指针。**
当执行`monitorenter`时，如果目标锁对象的计数器为零，那么说明它没有被其他线程所持有，Java 拟机会将该锁对象的持有线程设置为当前线程，并且将其计数器加1。
在目标锁对象的计数器不为零的情况下，如果锁对象的持有线程是当前线程，那么 Java 虚拟机可以将其计数器加1，否则需要等待直至持有线程释放该锁。
计数器为零代表锁已被释前当执行`monitorexit`时，Java虚拟机则需将锁对象的计数器减1。

### 死锁案例

```java
public static void main(String[] args) {
        final Object objectA = new Object();
        final Object objectB = new Object();

        new Thread(() -> {
            synchronized (objectA) {   
                System.out.println(Thread.currentThread().getName() + "\t" + "持有A锁，试图获取B锁");
                try {
                    Thread.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (objectB) {
                    System.out.println(Thread.currentThread().getName() + "\t" + "成功获取B锁");
                }
            }
        }, "A").start();

        new Thread(() -> {
            synchronized (objectB) {
                System.out.println(Thread.currentThread().getName() + "\t" + "持有B锁，试图获取A锁");
                try {
                    Thread.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (objectA) {
                    System.out.println(Thread.currentThread().getName() + "\t" + "成功获取A锁");
                }
            }
        }, "B").start();
    }
```

#### 如何排查死锁

##### 命名行方式

1. `jps -l ` 查询当前java进程的进程号

2. jstack 进程号

```shell
Java stack information for the threads listed above:
===================================================
"B":
        at DeadLockDemo.lambda$main$1(DeadLockDemo.java:32)
        - waiting to lock <0x0000000716ab6da0> (a java.lang.Object)
        - locked <0x0000000716ab6db0> (a java.lang.Object)   
        at DeadLockDemo$$Lambda$2/1023892928.run(Unknown Source)
        at java.lang.Thread.run(Thread.java:748)
"A":
        at DeadLockDemo.lambda$main$0(DeadLockDemo.java:18)
        - waiting to lock <0x0000000716ab6db0> (a java.lang.Object)
        - locked <0x0000000716ab6da0> (a java.lang.Object)
        at DeadLockDemo$$Lambda$1/1831932724.run(Unknown Source)
        at java.lang.Thread.run(Thread.java:748)

Found 1 deadlock.   # 发现死锁
#互相锁住
```

##### 图形化方式

`jconsole` 检查死锁

### 中断三大api

`interrupt`      `interrupted` (static)   `isInterrupted`

1. `interrupt`      设置线程的中断状态为true，发起一个协商而不会立刻停止线程   中断**不活动**的线程不会产生任何影响
2. `interrupted`   判断线程是否被中断并清除当前中断状态
   - 返回当前线程的中断状态，测试当前线程是否已被中断
   - 将当前线程的中断状态清零并重新设置为false，清除线程的中断状态

3. `isInterrupted` 判断当前线程是否被中断(通过检查中断标志位)

#### 中断标志位设置为true，是不是线程就立刻停止？

**不是**。中断只是一种协商机制，仅仅是修改中断标识位，不是立刻停止。

具体来说，当对一个线程，调用 interrupt() 时:

1. 如果线程处于正常活动状态，那么会将该线程的中断标志设置为 true，仅此而已。被设置中断标志的线程将继续正常运行，不受影响。所以， interrupt() 并不能真正的中断线程，需要被调用的线程自己进行配合才行。
2. 如果线程处于**被阻塞**状态(例如处于sleep,wait, join 等状态)，在别的线程中调用当前线程对象的interrupt方法,那么线程将**立即退出**被阻塞状态，并抛出一个`InterruptedException`异常。**中断状态将被清除**

```java
public class InterruptDemo {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            while (true) {
                if (Thread.currentThread().isInterrupted()) {
                    System.out.println(Thread.currentThread().getName() + "\t " +
                            "中断标志位：" + Thread.currentThread().isInterrupted());
                    break;
                }
//                try {
//                    Thread.sleep(200);
//                } catch (InterruptedException e) {
////                    Thread.currentThread().interrupt();
//                    e.printStackTrace();
//                }
                System.out.println("------ hello InterruptDemo");
            }
        }, "t1");
        t1.start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(() -> t1.interrupt(), "t2").start();

    }
}
/**
	1. 正常运行
	2. 加上注释 sleep  抛出InterruptedException  中断状态被清除 陷入死循环
	3. 需要在catch中重新设置标志位，程序抛出异常，正常停止
**/
```

#### 静态方法 `interrupted`   

```java
public class InterruptDemo {

    public static void main(String[] args) {
        System.out.println(Thread.currentThread().getName()+"\t "+Thread.interrupted());
        System.out.println(Thread.currentThread().getName()+"\t "+Thread.interrupted());
        System.out.println("********");
        Thread.currentThread().interrupt();
        System.out.println("********");
        System.out.println(Thread.currentThread().getName()+"\t "+Thread.interrupted());
        System.out.println(Thread.currentThread().getName()+"\t "+Thread.interrupted());
	
    }
}

/**
main	 false
main	 false
********
设置中断标志位
********
main	 true    获取标识位并清除标识位 false
main	 false
**/
```

### LockSupport

`locksupport`类可以阻塞当前线程以及唤醒指定被阻塞的线程

#### 三种让线程等待和唤醒的方法

#### Object类中的`wait`和`notify`方法实现线程等待和唤醒

```java
public class LockSupportDemo {

    public static void main(String[] args) {
        Object objectLock = new Object();
        new Thread(() -> {
            synchronized (objectLock) {
                System.out.println(Thread.currentThread().getName() + "\t  come in");
                try {
                    objectLock.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + "\t   被唤醒");
            }
        }, "t1").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(() -> {
            synchronized (objectLock) {
                System.out.println(Thread.currentThread().getName() + "\t   发出通知");
                objectLock.notify();
            }
        }, "t2").start();

    }
}
/**
   wait和notify  必须放置在synchronized块中  不然报错
   notify放在wait前面，线程无法被唤醒
   先wait在notify才正常
**/
```

#### Lock类的`await`和`signal`方法

```java
    public static void main(String[] args) {
        Lock lock = new ReentrantLock();
        Condition condition = lock.newCondition();
        new Thread(() -> {
            lock.lock();
            try {
                System.out.println(Thread.currentThread().getName() + "\t  come in");
                condition.await();
                System.out.println(Thread.currentThread().getName() + "\t   被唤醒");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        }, "t1").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(() -> {
            lock.lock();
            try {
                condition.signal();
                System.out.println(Thread.currentThread().getName() + "\t   发出通知");
            } finally {
                lock.unlock();
            }

        }, "t2").start();

    }

/**
   await和signal  必须放置在lock  unlock块中  不然报错
   signal放在await前面，线程无法被唤醒
   先await在signal才正常
**/
```

#### LockSupport类的`park`和`unpark`

```java
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "\t  come in");
            LockSupport.park();
 //           LockSupport.park();
            System.out.println(Thread.currentThread().getName() + "\t   被唤醒");
        }, "t1");
        t1.start();
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(() -> {
            LockSupport.unpark(t1);
//            LockSupport.unpark(t1);
//            LockSupport.unpark(t1);
//            LockSupport.unpark(t1);
            System.out.println(Thread.currentThread().getName() + "\t   发出通知");
        }, "t2").start();

    }

/**
   不需要放置在同步块中
   先后顺序可以调换
   成对使用   
   凭证最多累计1  unpark多次无效
**/
```

##### 为什么可以突破wait/notify的原有调用顺序?

因为unpark获得了一个凭证，之后再调用park方法，就可以名正言顺的凭证消费，故不会阻塞先发放了凭证后续可以畅通无阻。

##### 为什么唤醒两次后阻塞两次，但最终结果还会阻塞线程?

因为凭证的数量最多为 1，连续调用两次 unpark 和 用一次 unpark 效果一样，只会增加一个凭证:而调用两次 park却需要消费两个凭证，证不够，不能放行。

### JMM

#### 三大特性  **可见性**    **原子性**    **有序性** 

1. 可见性

​	当一个线程修改了某一个共享变量的值，其他线程能够立即知道该变更，JMM规定了所有的变量都存储在主内存中2

2. 原子性

​	一个操作是不可打断的，即多线程环境下，操作不能被其他线程干扰

3. 有序性

#### 多线程的读写

- 每个线程都有自己独立的工作内存，里面保存该线程使用到的变量的副本(主内存中该变量的一份拷贝)
- 线程对共享变最所有的操作都必须先在线程自己的工作内存中进行后写回主内存，不能直接从主内存中读写(不能越级)
- 不同线程之间也无法直接访问其他线程的工作内存中的变量，线程间变量值的传递需要通过主内存来进行(同级不能相互访问)

#### happens-before

### Volatile

可见性 

**volatile的写内存语义是直接刷新到主内存中，读的内存语义是直接从主内存中读取。**

有序性  **禁止指令重排**

#### 内存屏障

读屏障  load 和 写屏障 store

| 屏障类型   | 指令示例                  | 说明                                                         |
| ---------- | ------------------------- | ------------------------------------------------------------ |
| LoadLoad   | Load1;LoadLoad;Load2      | 保证load1的读操作在load2及后续读取操作之前执行               |
| StoreStore | Store1;StoreStore;Store2; | 在store2及其之后的写操作执行前，保证store1的写操作已刷回主内存 |
| LoadStore  | Load1;LoadStore;Store2;   | 在store2及其后的写操作执行前，保证load1的读操作已经读取结束  |
| StoreLoad  | Store1;StoreLoad;Load2    | 保证store1的写操作已刷新到主内存之后，load2及其后的读操作才能执行 |

### CAS

CAS的核心是Unsafe类

注意Unsafe类中的所有方法都是native修饰的 ，也就是说Unsafe类中的方法都直接调用操作系统底层资源执行相应任务

CAS是靠硬件实现的从而在硬件层面提升效率，最底层还是交给硬件来保证原子性和可见性

核心思想就是:比较要更新变量的值V和预期值E (compare)，相等才会将V的值设为新值N (swap)如果不相等自旋再来。

#### 缺点

1. 循环长时间 cpu开销大

2. ABA问题

​		+版本号  AtomicStampedReference  类 解决修改过几次

​			AtomicMarkableReference 解决是否修改过  true /false

countDownLatch

AtomicReferenceFieldUpdater   更新对象内部的某个属性

LongAdder  LongAdder的基本思路就是分散热点，将value值分散到一个Cel数组中，不同线程会命中到数组的不同槽中，各个线程只对自己槽中的那个值讲
行CAS操作，这样热点就被分散了，冲突的概率就小很多。如果要获取真正的long值，只要将各个槽中的变量值累加返回

LongAccumulator 

```java
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.atomic.AtomicLong;
import java.util.concurrent.atomic.LongAccumulator;
import java.util.concurrent.atomic.LongAdder;

class ClickNumber {
    int number = 0;

    public synchronized void clickBysynchronized() {
        number++;
    }

    AtomicLong atomicLong = new AtomicLong(0);

    public void clickByAtomicLong() {
        atomicLong.getAndIncrement();
    }

    LongAdder longAdder = new LongAdder();

    public void clickByLongAdder() {
        longAdder.increment();
    }

    LongAccumulator accumulator = new LongAccumulator((x, y) -> x + y, 0);

    public void clickByLongAccumulator() {
        accumulator.accumulate(1);
    }

}

public class AccumulatorDemo {

    public static final int _1w = 10000;
    public static final int threadNumber = 50;

    public static void main(String[] args) throws InterruptedException {

        ClickNumber clickNumber = new ClickNumber();
        long startTime = 0;
        long endTime = 0;

        CountDownLatch countDownLatch1 = new CountDownLatch(threadNumber);
        CountDownLatch countDownLatch2 = new CountDownLatch(threadNumber);
        CountDownLatch countDownLatch3 = new CountDownLatch(threadNumber);
        CountDownLatch countDownLatch4 = new CountDownLatch(threadNumber);

        startTime = System.currentTimeMillis();
        for (int i = 0; i < threadNumber; i++) {
            new Thread(() -> {
                try {
                    for (int j = 0; j < 100 * _1w; j++) {
                        clickNumber.clickBysynchronized();
                    }
                } finally {
                    countDownLatch1.countDown();
                }
            }, String.valueOf(i)).start();
        }
        countDownLatch1.await();
        endTime = System.currentTimeMillis();
        System.out.println("----cost Time:" + (endTime - startTime) + " 毫秒" + "\t clickBysynchronized" + clickNumber.number);


        startTime = System.currentTimeMillis();
        for (int i = 0; i < threadNumber; i++) {
            new Thread(() -> {
                try {
                    for (int j = 0; j < 100 * _1w; j++) {
                        clickNumber.clickByAtomicLong();
                    }
                } finally {
                    countDownLatch2.countDown();
                }
            }, String.valueOf(i)).start();
        }
        countDownLatch2.await();
        endTime = System.currentTimeMillis();
        System.out.println("----cost Time:" + (endTime - startTime) + " 毫秒" + "\t clickByAtomicLong" + clickNumber.atomicLong.get());

        startTime = System.currentTimeMillis();
        for (int i = 0; i < threadNumber; i++) {
            new Thread(() -> {
                try {
                    for (int j = 0; j < 100 * _1w; j++) {
                        clickNumber.clickByLongAdder();
                    }
                } finally {
                    countDownLatch3.countDown();
                }
            }, String.valueOf(i)).start();
        }
        countDownLatch3.await();
        endTime = System.currentTimeMillis();
        System.out.println("----cost Time:" + (endTime - startTime) + " 毫秒" + "\t clickByLongAdder" + clickNumber.longAdder.sum());

        startTime = System.currentTimeMillis();
        for (int i = 0; i < threadNumber; i++) {
            new Thread(() -> {
                try {
                    for (int j = 0; j < 100 * _1w; j++) {
                        clickNumber.clickByLongAccumulator();
                    }
                } finally {
                    countDownLatch4.countDown();
                }
            }, String.valueOf(i)).start();
        }
        countDownLatch4.await();
        endTime = System.currentTimeMillis();
        System.out.println("----cost Time:" + (endTime - startTime) + " 毫秒" + "\t clickByLongAccumulator" + clickNumber.accumulator.get());
    }
}
// 高并发下 LongAdder比AtomicLong 速度快
```



### ThreadLocal

**必须回收自定义的 ThreadLocal 变量，尤其在线程池场景下，线程经常会被复用，如果不清理自定义的 ThreadLocal 变量可能会影响后续业务逻辑和造成内存泄露等问题。尽量在代理中使用try-finally 块进行回收。**

```java
import java.util.Random;
import java.util.concurrent.TimeUnit;

class House {
    int saleCount = 0;

    public synchronized void saleCount() {
        saleCount++;
    }

    ThreadLocal<Integer> saleVolume = ThreadLocal.withInitial(() -> 0);

    public void saleVolumeByThreadLocal() {
        saleVolume.set(1 + saleVolume.get());
    }
}

public class ThreadLocalDemo {
    public static void main(String[] args) {
        House house = new House();
        for (int i = 0; i < 5; i++) {
            new Thread(() -> {
                int size = new Random().nextInt(5) + 1;
                try {
                    for (int j = 0; j < size; j++) {
                        house.saleCount();
                        house.saleVolumeByThreadLocal();
                    }
                    System.out.println(Thread.currentThread().getName() + "\t" + "号销售卖出：" + house.saleVolume.get());
                } finally {
                    house.saleVolume.remove();  // 使用结束后要remove  
                }

            }, String.valueOf(i)).start();
        }
        try {
            TimeUnit.MILLISECONDS.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "\t" + "共卖出" + house.saleCount);
    }
}
```

`Thread`  ` ThreadLocal `  `ThreadLocalMap`（弱引用  避免内存泄漏）

**不再会被使用的对象或者变量占用的内存不能被回收，就是内存泄露**

==用完记得remove==

### 内存布局和对象头

#### 对象的内存布局

在hotspot虚拟机中，对象在堆内存中的存储布局可以划分为三个部分: 对象头 (Header) 、实例数据(Instance Data)和对齐填充 (Padding ）。

##### 对象头

1. 对象标记 

​		哈希码、gc标记、gc次数、同步锁标记、偏向锁持有者     8字节

2. 类元信息（类型指针）

​	  64位机器中 占8字节

​	对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是哪个类的实例。

```java
People p = new People()
    People 类型指针
```

##### 实例数据

存放类的属性(Field)数据信息，包括父类的属性信息

##### 对齐填充

虚拟机要求对象起始地址必须是8字节的整数倍填充数据不是必须存在的，仅仅是为了字节对齐这部分内存按8字节补充对齐。

### Synchronized与锁升级

无锁

偏向锁: MarkWord存储的是偏向的线程ID

轻量锁: MarkWord存储的是指向线程栈中Lock Record的指针

重量锁: MarkWord存储的是指向堆中的monitor对象的指针;

#### 偏向锁：单线程竞争 

 锁标记 101

当一段同步代码一直被同一个线程多次访问，由于只有一个线程那么该线程在后续访问时便会自动获得锁

**java15后逐步废弃偏向锁**

#### 轻量锁

多线程竞争，但是任意时刻最多只有一个线程竞争，即不存在锁竞争太过激烈的情况，也就没有线程阻寨。**本质是自旋锁CAS**

锁标记 00

自旋达到一定次数升级

自适应自旋锁的大致原理：

​	线程如果自旋成功了，那下次自旋的最大次数会增加，因为JVM认为既然上次成功了，那么这一次也很大概率会成功。反之，如果很少	会自旋成功，那么下次会减少自旋的次数甚至不自旋，避免CPU空转。

#### 重量锁

有大量的线程参与锁的竞争，冲突性很高

锁标记 10

#### 锁升级之后hsahcode

计算一致性哈希之后，无法进入偏向状态，直接升级成轻量级锁

偏向锁过程中遇到一致性哈希计算请求，立马撤销偏向模式，膨胀为重量级锁

|    锁    |                             优点                             |                      缺点                      |             适用场景             |
| :------: | :----------------------------------------------------------: | :--------------------------------------------: | :------------------------------: |
|  偏向锁  | 加锁和解锁不需要额外的消耗，和执行非同步方法相比仅存在纳秒级的差距 | 如果线程间存在锁竞争，会带来额外的锁撤销的消耗 | 适用于只有一个线程访问同步块场景 |
| 轻量级锁 |           竞争的线程不会阻塞，提高了程序的响应速度           | 如果始终得不到锁竞争的线程，使用自旋会消耗 CPU | 追求响应时间同步块执行速度非常快 |
|  重量锁  |               线程竞争不使用自旋，不会消耗 CPU               |             线程阻塞，响应时间缓慢             |   追求吞吐量同步块执行速度较长   |

`JIT`锁消除、锁粗化

### AQS

是用来实现锁或者其它同步器组件的公共基础部分的抽象实现，是重量级基础框架及整个`JUC`体系的基石，主要用于解决锁分配给"谁"的问题。

`FIFO`等待队列    实现 CLH双向队列

1. `tryAcquire(arg)`
2. `addWaiter(Node.EXCLUSIVE)`
3. `acquireQueued(addWaiter(Node.EXCLUSIVE), arg)`
