# Java多线程基础

>主要介绍了
>
>​	1.Java的多线程编程支持,简要介绍了线程的基本概念，并讲解了线程和进程之间的区别和联系。
>
>​	2.讲解了如何创建、启动多线程，并对比了两种创建多线程之间的优势和劣势，也详细介绍了线程的生命周期。
>
>​	3.还通过示例程序示范了控制线程的几个方法，还详细讲解了线程同步的意义和必要性，并介绍了两种不同的线程同步的方式。
>
>​	4.另外也介绍了线程通信的方式。还对单例设计模式做了详细的讲解，使用了多种方式进行实现，这个可是在企业面试题中的一个高频面试题



![image-20200604141345032](C:\Users\a1043\AppData\Roaming\Typora\typora-user-images\image-20200604141345032.png)

## 1.相关的概念

### 程序（Program）

	>为了实现一个功能，完成一个任务而选择一种编程语言编写的一组指令的集合。

​	

### 进程（Process）

	>程序的一次执行过程。操作系统会给这个进程分配资源（内存）。进程是操作系统分配资源的最小单位。
	>
	>​	进程与进程之间的内存是独立，无法直接共享。最早的DOS操作系统是单任务的，同一时间只能运行一个进程。后来现在的操作系统都是支持多任务的，可以同时运行多个进程。进程之间来回切换。成本比较高。

​	几乎所有操作系统都支持进程的概念，所有运行中的任务通常对应一条进程（Process）。当一个程序进入内存运行，即变成一个进程。进程是处于运行过程中的程序，并且具有一定独立功能，进程是操作系统进行资源分配和调度的一个独立单位。		

​	进程一般包含三个特性：独立性，动态性，并发性

- 独立性：

  - 进程是操作系统进行资源分配和调度的一个独立单位，每一个进程都拥有自己私有的地址空间。在没有经过进程本身允许的情况下，一个用户进程不可以直接访问其他进程的地址空间。哪怕在同一台计算机上运行，进程之间通信可能也需要通过网络、独立于进程的文件来交换数据。

- 动态性：

  - 程序只是一个静态的指令的集合，而进程是一个正在系统中运行的活动的指令集合，即进程是处于运行过程中的程序。
  - 在进程中加入了时间的概念，进程具有自己的生命周期和各种不同状态，这些概念在程序中都是不具备的。

- 并发性：

  - 多个进程可以在单个处理器上并发执行，多个进程之间不会相互影响。现代的操作系统都支持多进程的并发，但在具体的实现细节上可能因为硬件和操作系统的不同而采用不同的策略，目前大多数采用效率更高的抢占式多任务策略。

  - 对于一个CPU而言，它在某个时间点上只能执行一个程序，也就是只能运行一个进程，CPU不断的在这些进程之间轮换执行。那么，我们为什么可以一边用开发工具写程序，一边听音乐，一边还上网查资料.....，我们没有感觉到中断和轮换呢？这是因为CPU的执行速度相对于我们的感知速度来说实在是太快了，所以我们感觉像是同时运行一样。但是当我们启动足够多的程序时，依然可以感觉到运行速度下降的。

    

### 线程（Thread）

	>线程是进程中的其中一条执行路径。一个进程中至少有一个线程，也可以有多个线程。线程是CPU执行和调度的最小单元。
	>
	>​	同一个进程的多个线程之间有些内存是可以共享的（方法区、堆），也有些内存是独立的（栈（包括虚拟机栈和本地方法栈）、程序计数器）。
	>
	>线程调度和执行的单位，每个线程拥有独立的运行栈和程序计数器
	>
	>线程之间的切换相对进程来说成本比较低。

- 多线程则扩展了多进程的概念，使得通一个进程可以同时并发处理多个任务。当进程被初始化后，主线程就被创建了，对于Java程序来说，main线程就是主线程，但我们可以在该进程内创建多条顺序执行路径，这些独立的顺序执行路径就是线程。
- 进程中的每一个线程可以完成一定的任务，并且是独立的，线程可以拥有自己独立的堆栈、自己的程序计数器和自己的局部变量，但不再拥有系统资源，它与父进程的其他线程共享该进程所拥有的全部资源。
- 由于线程间的通信是在同一个地址空间上进行的，所以不需要额外的通信机制，这就使得通信更简便而且信息传递的速度也更快，因此可以通过简单编程实现多线程相互协同来完成进程所要完成的任务。但是也存在安全问题，因为其中一个线程对共享的系统资源的操作都会给其他线程带来影响，由此可知，多线程中的同步是非常重要的问题。
- 线程的执行也是抢占式的，也就是说，当前运行的线程在任何时候都可能被挂起，以便另一个线程可以运行。我们说CPU在不同的进程之间轮换，进程又在不同的线程之间轮换，因此线程是CPU执行和调度的最小单元。
- 一个程序运行后至少有一个进程，一个进程里可以包含多个线程，但至少要包含一个线程。当操作系统创建一个进程时，必须为该进程分配独立的内存空间，并分配大量的相关资源；但创建一个线程简单的多，而且多个线程共享同一个进程的虚拟空间，所以，使用多线程来实现并发比使用多进程实现并发的性能要高的多。

### 并行（parallel）

> 多个处理器同时可以执行多条执行路径。在同一时刻，有多条指令在多个处理器上同时执行。多个人同时做不同的事

​	指将大量的任务，拆解成多个子任务分配到多个线程上，并发的执行。比如你要计算1到100的和，可以将计算分成两个部分，一个线程计算1到50的和，另一个线程计算51到100的和。

### 并发（concurrency）

> 多个任务同时执行，但是可能存在先后关系。在同一个时刻只能有一条指令执行，但多个进程的指令被快速轮换执行，使得在宏观上具有多个进程同时执行的效果。
>
> 一个CPU采用时间片的方式 同时执行多个任务。比如秒杀，多个人做同一件事

​	并发指在同一时刻做不止一件事情。比如数据库处理请求时，接受了第一个请求但是未处理完成，此时也可以接受第二个请求。两个请求任务在处理时间节点上可以有交集。

> 简单的说：**并行是多线程的一种形式，多线程是并发的一种形式。**异步也是并发的一种形式。

### 串行

> 串行是指多个任务时，各个任务按顺序执行，完成一个之后才能进行下一个。



## 2. 两种实现多线程的方式

### 继承Thread类

> 步骤：
>
> 1. 编写线程类，去继承Thread类
> 2. 重写public void run(){}
> 3. 创建线程对象
> 4. 调用start()

```java
class MyThread extends Thread {
    public void run(){
        //...
    }
}
class Test{
    public static void main(String[] args){
        MyThread my = new MyThread();
        my.start();//有名字的线程对象启动
        new MyThread().start();//匿名线程对象启动
        
        //匿名内部类的匿名对象启动
        new Thread(){
            public void run(){
                //...
            }
        }.start();
        
        //匿名内部类，但是通过父类的变量多态引用，启动线程
        Thread t =  new Thread(){
            public void run(){
                //...
            }
        };
        t.start();
    }
}
```



### 实现Runnable接口

> 步骤：
>
> 1. 编写线程类，实现Runnable接口
> 2. 重写public void run(){}
> 3. 创建线程对象
> 4. 借助Thread类的对象启动线程

```java
class MyRunnable implements Runnable{
    public void run(){
        //...
    }
}
class Test {
    public static void main(String[] args){
        MyRunnable my = new MyRunnable();
        Thread t1 = new Thread(my);
        Thread t2 = new Thread(my);
        t1.start();
        t2.start();
        
        //两个匿名对象
        new Thread(new MyRunnable()).start();
        
        //匿名内部类的匿名对象作为实参直接传给Thread的构造器
        new Thread(new Runnable(){
            public void run(){
                //...
            }
        }).start();
            
    }
}
```





## 3. 线程的生命周期

>一个完整的生命周期中通常要经历如下的五种状态：新建（New）、就绪（Runnable）、运行（Running）、阻塞（Blocked）、死亡（Dead）。CPU需要在多条线程之间切换，于是线程状态会多次在运行、阻塞、就绪之间切换
>
>​	**新建：** 当一个Thread类或其子类的对象被声明并创建时，新生的线程对象处于新建状态
>
>​	**就绪：**处于新建状态的线程被start()后，将进入线程队列等待CPU时间片，此时它已具备了运行的条件，只是没分配到CPU资源
>
>​	**运行：**当就绪的线程被调度并获得CPU资源时,便进入运行状态， run()方法定义了线程的操作和功能
>
>​	**阻塞：**在某种特殊情况下，被人为挂起或执行输入输出操作时，让出 CPU 并临时中止自己的执行，进入阻塞状态
>
>​	**死亡：**线程完成了它的全部工作或线程被提前强制性地中止或出现异常导致结束  

```java
//Thread api中枚举类定义
public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
        NEW,

        /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,

        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,

        /**
         * Thread state for a waiting thread.
         * A thread is in the waiting state due to calling one of the
         * following methods:
         * <ul>
         *   <li>{@link Object#wait() Object.wait} with no timeout</li>
         *   <li>{@link #join() Thread.join} with no timeout</li>
         *   <li>{@link LockSupport#park() LockSupport.park}</li>
         * </ul>
         *
         * <p>A thread in the waiting state is waiting for another thread to
         * perform a particular action.
         *
         * For example, a thread that has called <tt>Object.wait()</tt>
         * on an object is waiting for another thread to call
         * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
         * that object. A thread that has called <tt>Thread.join()</tt>
         * is waiting for a specified thread to terminate.
         */
        WAITING,

        /**
         * Thread state for a waiting thread with a specified waiting time.
         * A thread is in the timed waiting state due to calling one of
         * the following methods with a specified positive waiting time:
         * <ul>
         *   <li>{@link #sleep Thread.sleep}</li>
         *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
         *   <li>{@link #join(long) Thread.join} with timeout</li>
         *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
         *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
         * </ul>
         */
        TIMED_WAITING,

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
        TERMINATED;
    }
```

![1559782705811](D:/tutorial/Java后端开发/尚硅谷javaEE 19年10月毕业班/01java基础（基础看这个，柴林燕讲的比刘优好）/day25_新特性/imgs/1559782705811.png)

![image-20200603191142989](C:\Users\a1043\AppData\Roaming\Typora\typora-user-images\image-20200603191142989.png)





## 4. Java.lang.Thread相关 API

### 创建线程对象相关

**构造器**

1. **Thread()：**创建新的Thread对象
2. **Thread(String name)：**创建线程并指定线程实例名
3. **Thread(Runnable target)：**指定创建线程的目标对象，它实现了Runnable接口中的run方法
4. **Thread(Runnable target, String name)：**创建新的Thread对象

**编写线程体和启动线程**

1. **public void run()：**子类必须重写run()以编写线程体
2. **public void start()：**启动线程

### 获取和设置线程信息相关

1. public static Thread currentThread()：这是一个静态方法，总是返回当前在执行的线程对象

2. public final boolean isAlive()：测试线程是否处于活动状态。如果线程已经启动且尚未终止，则为活动状态。

3. public final String getName()：getName()方法是Thread的实例方法，该方法返回当前线程对象的名字，可以通过

4. public final void setName(String name)：设置该线程名称。除了主线程main之外，其他线程可以在创建时指定线程名称或通过setName(String name)方法设置线程名称，否则依次为Thread-0，Thread-1......等。

5. public final int getPriority() ：返回线程优先值

6. public final void setPriority(int newPriority) ：改变线程的优先级

  > 每个线程都有一定的优先级，优先级高的线程将获得较多的执行机会。每个线程默认的优先级都与创建它的父线程具有相同的优先级。Thread类提供了setPriority(int newPriority)和getPriority()方法类设置和获取线程的优先级，其中setPriority方法需要一个整数，并且范围在[1,10]之间，通常推荐设置Thread类的三个优先级常量：
  >
  > - MAX_PRIORITY（10）：最高优先级 
  > - MIN _PRIORITY （1）：最低优先级
  > - NORM_PRIORITY （5）：普通优先级，默认情况下main线程具有普通优先级。
  >
  > 优先级只是影响概率。

  

### 控制线程相关

>Thread类提供了一些方法，可以控制线程的执行。

- **线程休眠：Thread.sleep(毫秒)**

  如果当我们需要让当前正在执行的线程暂停一段时间，则可以通过调用Thread类的静态sleep()方法，它会导致当前线程进入阻塞状态：

  ​	public static void sleep(long millis)：在指定的毫秒数内让当前正在执行的线程休眠（暂停执行），此操作受到系统计时器和调度程序精度和准确性的影响。

  ​	public static void sleep(long millis,int nanos)：在指定的毫秒加纳秒内让当前正在执行的线程休眠（暂停执行），此操作受到系统计时器和调度程序精度和准确性的影响。

  

- **打断线程：interrupt()**

- **暂停当前线程：Thread.yield()**

  yield()也是一个静态方法，它可以让当前正在执行的线程暂停，但它不会阻塞该线程，它只是将该线程转入就绪状态。

  yield只是让当前线程暂停一下，让系统的线程调度器重新调度一次，希望优先级与当前线程相同或更高的其他线程能够获得执行机会，**但是这个不能保证，**完全有可能的情况是，当某个线程调用了yield方法暂停之后，线程调度器又将其调度出来重新执行。

  

- **线程要加塞：join()**

> B.join()这句代码写在A线程体中，A线程被加塞，直到B结束，和其他线程无关。

当在某个线程的线程体中调用了另一个线程的join()方法，当前线程将被阻塞，直到join进来的线程执行完它才能继续。

​	void join() ：等待该线程终止。

​	void join(long millis) ：等待该线程终止的时间最长为 millis 毫秒。如果millis时间到，将不再等待。

​	void join(long millis, int nanos) ：等待该线程终止的时间最长为 millis 毫秒 + nanos 纳秒。 

- **守护线程**

  >有一种线程，它是在后台运行的，它的任务是为其他线程提供服务的，这种线程被称为“守护线程”。JVM的垃圾回收线程就是典型的守护线程。
  >
  >​	守护线程有个特点，就是如果所有非守护线程都死亡，那么守护线程自动死亡。

  调用**setDaemon(true)**方法可将指定线程设置为守护线程。必须在线程启动之前设置，否则会报IllegalThreadStateException异常。

  调用**isDaemon()**可以判断线程是否是守护线程。

  ```java
  public class TestDaemon {
  	public static void main(String[] args) {
  		MyDaemon m = new MyDaemon();//
  		m.setDaemon(true);//
  		m.start();
  		
  		for (int i = 0; i < 20; i++) {
  			System.out.println("main:" + i);
  		}
  	}
  }
  class MyDaemon extends Thread{
  	public void run(){
  		while(true){
  			System.out.println("MyDaemon");
  			try {
  				Thread.sleep(1);
  			} catch (InterruptedException e) {
  				e.printStackTrace();
  			}
  		}
  	}
  }
  ```

  

- **停止线程**

  我们知道线程体执行完，或遇到未捕获的异常自然会停止，但是有时我们希望由另一个线程检测到某个情况时，停止一个线程，该怎么做呢？

  Thread类提供了**stop()**来停止一个线程，但是该方法具有固有的不安全性，已经标记为**@Deprecated**不建议再使用，那么我们就需要通过其他方式来停止线程了，其中一种方式是使用标识。

>**volatile**的作用是确保不会因编译器的优化而省略某些指令，volatile的变量是说这变量可能会被意想不到地改变，每次都小心地重新读取这个变量的值，而不是使用保存在寄存器里的备份，这样，编译器就不会去假设这个变量的值了。



## 5. 线程安全问题与线程同步

>​	多线程编程是有趣且复杂的事情，它常常容易突然出现“错误情况”，这是由于系统的线程调度具有一定的随机性。即使程序在运行过程中偶尔会出现问题，那也是由于我们的代码有问题导致的。当多个线程访问同一个数据时，非常容易出现线程安全问题。

### 线程安全问题

>### 什么情况下会发生线程安全问题？
>
>（1）多个线程
>
>（2）共享数据
>
>（3）多个线程的线程体中，多条语句再操作这个共享数据时

**问题1：多个线程对账本的共享，会造成操作的不完整性，会破坏数据。**

![image-20200604151841581](C:\Users\a1043\AppData\Roaming\Typora\typora-user-images\image-20200604151841581.png)

​	解决思路：其中一个线程进入判断逻辑之后，才能允许另一个线程进入



**问题2**：**模拟火车站售票程序，开启多个窗口同时卖票（假设3个）**

![image-20200604152313436](C:\Users\a1043\AppData\Roaming\Typora\typora-user-images\image-20200604152313436.png)

![image-20200604152505059](C:\Users\a1043\AppData\Roaming\Typora\typora-user-images\image-20200604152505059.png)

​	解决思路：当一个窗口没有操作完的时候，其他窗口必须等待



### 关键字：volatile

> volatile：易变，不稳定，不一定什么时候会变

修饰：成员变量

作用：当多个线程同时去访问的某个成员变量时，而且是频繁的访问，再多次访问时，发现它的值没有修改，Java执行引擎就会对这个成员变量的值进行缓存。一旦缓存之后，这个时候如果有一个线程把这个成员变量的值修改了，Jav执行引擎还是从缓存中读取，导致这个值不是最新的。如果不希望Java执行引擎把这个成员变的值缓存起来，那么就可以在成员变量的前面加volatile，每次用到这个成员变量时，都是从主存中读取。

### 关键字：synchronized（同步）



### 如何解决线程(继承和runnable)安全问题？

> - 同步代码块
>
> - 同步方法
>
>   同步的弊端：解决了线程安全问题，但是操作共享数据的过程中 是单线程的，效率不高
>
> - JDK5新增Lock锁的方式

#### **同步代码块**

>同步监视器 俗称 锁 ，任何一个类的对象都可以当锁，多个线程必须共用同一个锁
>
>获得了同步件监视器的线程可以操作 共享数据，其他线程则 阻塞等待，直到线程释放了锁
>
>- 选择共享资源对象 为 同步监视器对象
>- 选择this对象 为 同步监视器对象
>- 使用反射中Class的对象（即当前类.class） 为 同步监视器对象

```java
synchronized(同步监视器对象){
    //需要被同步的代码(操作共享数据的代码)，一次任务代码，这其中的代码，在执行过程中，不希望其他线程插一脚
}

//Java程序运行使用任何对象来作为同步监视器对象，只要保证共享资源的这几个线程，锁的是同一个同步监视器对象即可。
锁对象：
（1）任意类型的对象
（2）确保使用共享数据的这几个线程，使用同一个锁对象
```

**同步原理解决卖票问题**

![image-20200604154733190](C:\Users\a1043\AppData\Roaming\Typora\typora-user-images\image-20200604154733190.png)





#### 同步方法

>Java的多线程安全支持还提供了同步方法，同步方法就是使用synchronized关键字来修饰某个方法，则该方法称为同步方法。对于同步方法而言，无须显式指定同步监视器，静态方法的同步监视器对象是当前类的Class对象，非静态方法的同步监视器对象是调用当前方法的this对象。

```java
synchronized 修饰符 返回值类型  方法名(形参列表)throws 异常列表{
    //操作共享数据的代码完整的声明这个方法中，同一时间，只能有一个线程能进来操作共享数据
}

同步监视器对象（锁）：
（1）非静态方法：this（谨慎）
（2）静态方法：当前类的Class对象
```



#### 何时释放同步监视器的锁定？

>- 当前线程的同步方法、同步代码块执行结束。
>- 当前线程在同步代码块、同步方法中遇到break、return终止了该代码块、该方法的继续执行。
>- 当前线程在同步代码块、同步方法中出现了未处理的Error或Exception，导致当前线程异常结束。
>- 当前线程在同步代码块、同步方法中执行了锁对象的wait()方法，当前线程被挂起，并释放锁。



#### 死锁（DeadLock）

>不同的线程 分别锁住 对方需要的 锁对象 不释放，都在等待对方先放弃时 形成了 线程的死锁。
>
>一旦出现死锁，整个程序既不会发生异常，也不会给出任何提示，只是所有线程处于阻塞状态，无法继续。

![img](https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=3169601855,1279804081&fm=11&gp=0.jpg)

#### Lock锁（ReentrantLock）

>从JDK 5.0开始，Java提供了更强大的线程同步机制——通过显式定义同步锁对象来实现同步。同步锁使用Lock对象充当。
>
>java.util.concurrent.locks.Lock接口是控制多个线程对共享资源进行访问的工具。锁提供了对共享资源的独占访问，每次只能有一个线程对Lock对象加锁，线程开始访问共享资源之前应先获得Lock对象。
>
>ReentrantLock 类实现了 Lock ，它拥有与 synchronized 相同的并发性和内存语义，在实现线程安全的控制中，比较常用的是ReentrantLock，可以显式加锁、释放锁。

```java
class A{
	private final ReentrantLock lock = new ReenTrantLock();//
	public void m(){
		lock.lock();//
		try{
			//保证线程安全的代码;
		}
		finally{
			lock.unlock();  //
		}
	}
}

//注意：如果同步代码有异常，要将unlock()写入finally语句块
```



### **synchronized** **与** **Lock** **的对比**

- Lock是显式锁（手动开启和关闭锁，别忘记关闭锁），synchronized是隐式锁，出了作用域自动释放
- Lock只有代码块锁，synchronized有代码块锁和方法锁
- 使用Lock锁，JVM将花费较少的时间来调度线程，性能更好。并且具有更好的扩展性（提供更多的子类）

**优先使用顺序：**

 Lock  >>  同步代码块（已经进入了方法体，分配了相应资源 ) >> 同步方法（在方法体之外）



## 6. 线程通信

### **使用两个线程交替打印** 1-100

### 生产者与消费者问题

> 生产者(Productor)将产品交给店员(Clerk)，而消费者(Customer)从店员处取走产品，店员一次只能持有固定数量的产品(比如:20），如果生产者试图生产更多的产品，店员会叫生产者停一下，如果店中有空位放产品了再通知生产者继续生产；如果店中没有产品了，店员会告诉消费者等一下，如果店中有产品了再通知消费者来取走产品
>
> ​	这里出现两个问题：
>
> ​			生产者比消费者快时，消费者会漏掉一些数据没有取到。
>
> ​			消费者比生产者快时，消费者会取相同的数据。

分析问题：

​	

1、为了解决“生产者与消费者问题”。

当一些线程负责往“数据缓冲区”放数据，另一个线程负责从“数据缓冲区”取数据。

问题1：生产者线程与消费者线程使用同一个数据缓冲区，就是共享数据，那么要考虑同步

问题2：当数据缓冲区满的时候，生产者线程需要wait()， 当消费者消费了数据后，需要notify或notifyAll

​		当数据缓冲区空的时候，消费者线程需要wait()， 当生产者生产了数据后，需要notify或notifyAll

2、java.lang.Object类中声明了：

（1）wait()：必须由“同步锁”对象调用

（2）notfiy()和notifyAll()：必须由“同步锁”对象调用



3、面试题：sleep()和wait的区别

（1）sleep()不释放锁，wait()释放锁

（2）sleep()在Thread类中声明的，wait()在Object类中声明

（3）sleep()是静态方法，是Thread.sleep()

​        wait()是非静态方法，必须由“同步锁”对象调用

（4）sleep()方法导致当前线程进入阻塞状态后，当时间到或interrupt()醒来

​	 wait()方法导致当前线程进入阻塞状态后，由notify或notifyAll()



4、哪些操作会释放锁？

（1）同步代码块或同步方法正常执行完一次自动释放锁

（2）同步代码块或同步方法遇到return等提前结束

（3）wait()



5、不释放锁

（1）sleep()

（2）yield()

（3）suspend()

### sleep()与wait()异同



## 7. 单例设计模式