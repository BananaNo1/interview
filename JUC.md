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

1. `interrupt`      设置线程的中断状态为true，发起一个协商而不会立刻停止线程
2. `interrupted`   判断线程是否被中断并清除当前中断状态
   - 返回当前线程的中断状态，测试当前线程是否已被中断
   - 将当前线程的中断状态清零并重新设置为false，清除线程的中断状态

3. `isInterrupted` 判断当前线程是否被中断(通过检查中断标志位)