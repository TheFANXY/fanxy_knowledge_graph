# 1 什么是 JUC

## 1.1 JUC 简介 

**这部分建议结合JAVA基础的多线程一起看**

在 Java 中，线程部分是一个重点，本篇文章说的 JUC 也是关于线程的。JUC 就是 java.util.concurrent 工具包的简称。这是一个处理线程的工具包，JDK 1.5 开始出现的。



## 1.2 进程与线程 

- **程序（program）**：为完成特定任务，用某种语言编写的`一组指令的集合`。**即指`一段静态的代码`，静态对象。**
- **进程（process）**：**程序的一次执行过程，或是正在内存中运行的应用程序。**如：运行中的QQ，运行中的网易音乐播放器。
  - **每个进程都有一个独立的内存空间，系统运行一个程序即是一个进程从创建、运行到消亡的过程。（生命周期）**
  - **程序是静态的，进程是动态的**
  - **进程作为`操作系统调度和分配资源的最小单位`（亦是系统运行程序的基本单位）**，**系统在运行时会为每个进程分配不同的内存区域。**
  - 现代的操作系统，大都是支持多进程的，支持同时运行多个程序。比如：现在我们上课一边使用编辑器，一边使用录屏软件，同时还开着画图板，dos窗口等软件。
- **线程（thread）**：**进程可进一步细化为线程，是程序内部的`一条执行路径`。一个进程中至少有一个线程。**
  - **一个进程同一时间若`并行`执行多个线程**，**就是支持多线程的。**
  - **线程作为`CPU调度和执行的最小单位`。**
  - **一个进程中的多个线程共享相同的内存单元，它们从同一个堆中分配对象，可以访问相同的变量和对象。**这就使得线程间通信更简便、高效。**但多个线程操作共享的系统资源可能就会带来`安全的隐患`。**

**不同的进程之间是不共享内存的。**

**进程之间的数据交换和通信的成本很高。**



## 1.3 线程的状态 

### 1.3.1 线程状态枚举类 

![1](./1 JUC基础篇.assets/b-1.png)

Thread.State

- **`NEW（新建）`：**线程刚被创建，但是并未启动。还没调用start方法。
- **`RUNNABLE（可运行）`：**这里没有区分就绪和运行状态。因为**对于Java对象来说，只能标记为可运行**，至于**什么时候运行**，不是JVM来控制的了，是**OS来进行调度的**，而且时间非常短暂，因此对于Java对象的状态来说，无法区分。
- **`Teminated（被终止）`：**表明此线程已经**结束生命周期，终止运行**。
- 重点说明，根据**`Thread.State`**的定义，**阻塞状态分为三种**：**`BLOCKED`、`WAITING`、`TIMED_WAITING`。**
  - **`BLOCKED（锁阻塞）`**：在API中的介绍为：一个正在阻塞、等待一个监视器锁（锁对象）的线程处于这一状态。只有获得锁对象的线程才能有执行机会。
    - 比如，线程A与线程B代码中使用同一锁，如果线程A获取到锁，线程A进入到Runnable状态，那么线程B就进入到Blocked锁阻塞状态。
  - **`TIMED_WAITING（计时等待）`**：在API中的介绍为：一个正在限时等待另一个线程执行一个（唤醒）动作的线程处于这一状态。
    - 当前线程执行过程中遇到Thread类的`sleep`或`join`，Object类的`wait`，LockSupport类的`park`方法，并且在调用这些方法时，`设置了时间`，那么当前线程会进入TIMED_WAITING，直到时间到，或被中断。
  - **`WAITING（无限等待）`**：在API中介绍为：一个正在无限期等待另一个线程执行一个特别的（唤醒）动作的线程处于这一状态。
    - 当前线程执行过程中遇到遇到Object类的`wait`，Thread类的`join`，`LockSupport`类的`park`方法，并且在调用这些方法时，`没有指定时间`，那么当前线程会进入WAITING状态，直到被唤醒。
      - 通过Object类的wait进入WAITING状态的要有Object的notify/notifyAll唤醒；
      - 通过Condition的await进入WAITING状态的要有Condition的signal方法唤醒；
      - 通过`LockSupport`类的park方法进入WAITING状态的要有LockSupport类的unpark方法唤醒
      - 通过Thread类的join进入WAITING状态，只有调用join方法的线程对象结束才能让当前线程恢复；

说明：当从WAITING或TIMED_WAITING恢复到Runnable状态时，如果发现当前线程没有得到监视器锁，那么会立刻转入BLOCKED状态。



### 1.3.2 wait/sleep 的区别 

（1）sleep 是 Thread 的静态方法，wait 是 Object 的方法，任何对象实例都能调用。

（2）sleep 不会释放锁，它也不需要占用锁。wait 会释放锁，但调用它的前提是当前线程占有锁(即代码要在 synchronized 中)。

（3）它们都可以被 interrupted 方法中断。



## 1.4 并发与并行 

**并行（parallel）**：**指两个或多个事件在`同一时刻`发生（同时发生）。指在同一时刻，有`多条指令`在`多个CPU`上`同时`执行。**比如：多个人同时做不同的事。

![2.png](./1 JUC基础篇.assets/b-2.png)

 **并发（concurrency）**：**指两个或多个事件在`同一个时间段内`发生。即在一段时间内，有`多条指令`在`单个CPU`上`快速轮换、交替`执行，使得在宏观上具有多个进程同时执行的效果。**

![3.png](./1 JUC基础篇.assets/b-3.png)

**在操作系统中，启动了多个程序，`并发`指的是在一段时间内宏观上有多个程序同时运行，这在单核 CPU 系统中，每一时刻只能有一个程序执行，即微观上这些程序是分时的交替运行，只不过是给人的感觉是同时运行，那是因为分时交替运行的时间是非常短的。**

而在**多核 CPU 系统**中，则这些可以**`并发`执行的程序便可以分配到多个CPU上**，实现多任务并行执行，**即利用每个处理器来处理一个可以并发执行的程序，这样多个程序便可以同时执行。**目前电脑市场上说的多核 CPU，便是多核处理器，**核越多，`并行`处理的程序越多，能大大的提高电脑运行的效率。**

**要解决大并发问题，通常是将大任务分解成多个小任务**, 由于操作系统对进程的调度是随机的，所以切分成多个小任务后，可能会从任一小任务处执行。这可能会出现一些现象：

• 可能出现一个小任务执行了多次，还没开始下个任务的情况。这时一般会采用队列或类似的数据结构来存放各个小任务的成果

• 可能出现还没准备好第一步就执行第二步的可能。这时，一般采用多路复用或异步的方式，比如只有准备好产生了事件通知才执行某个任务。

• 可以多进程/多线程的方式并行执行这些小任务。也可以单进程/单线程执行这些小任务，这时很可能要配合多路复用才能达到较高的效率



## 1.5 管程 

管程(monitor)是保证了同一时刻只有一个进程在管程内活动,即管程内定义的操作在同一时刻只被一个进程调用(由编译器实现)

但是这样并不能保证进程以设计的顺序执行 JVM 中同步是基于进入和退出管程(monitor)对象实现的，每个对象都会有一个管程(monitor)对象，管程(monitor)会随着 java 对象一同创建和销毁执行线程首先要持有管程对象，然后才能执行方法，当方法完成之后会释放管程，方法在执行时候会持有管程，其他线程无法再获取同一个管程



## 1.6 用户线程和守护线程 

**用户线程**:平时用到的普通线程,自定义线程

**守护线程**:运行在后台,是一种特殊的线程,比如垃圾回收

**当主线程结束后**,用户线程还在运行, JVM **存活**

**如果没有用户线程**,都是守护线程, JVM **结束**

```java
public class ThreadTest {

    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + " :: " + Thread.currentThread().isDaemon());
        }, "线程1");
		
       	// 如果注释掉这句 会发现主线程结束的时候 整个进程没有结束 
        thread.setDaemon(true);
        thread.start();
        System.out.println(Thread.currentThread().getName());
        Thread.sleep(3000);
    }
}
```



# 2 Lock 接口 

## 2.1 Synchronized 

### 2.1.1 Synchronized 关键字回顾 

synchronized 是 Java 中的关键字，是一种同步锁。它修饰的对象有以下几种：

1. 修饰一个代码块，被修饰的代码块称为同步语句块，其作用的范围是大括号{}括起来的代码，作用的对象是调用这个代码块的对象；

2. 修饰一个方法，被修饰的方法称为同步方法，其作用的范围是整个方法，作用的对象是调用这个方法的对象；

- 虽然可以使用 synchronized 来修饰方法，但 synchronized 并不属于方法定义的一部分，因此，synchronized 关键字不能被继承。如果在父类中的某个方法使用了 synchronized 关键字，而在子类中覆盖了这个方法， **在子类中的这个方法默认情况下并不是同步的** ，而必须显式地在子类的这个方法中加上synchronized 关键字才可以。
- **还可以在子类方法中调用父类中相应的方法，这样虽然子类中的方法不是同步的，但子类调用了父类的同步方法，因此，子类的方法也就相当于同步了。**

3. 修饰一个静态的方法，其作用的范围是整个静态方法，作用的对象是这个类的所有对象；

4. 修饰一个类，其作用的范围是 synchronized 后面括号括起来的部分，作用主的对象是这个类的所有对象。



### 2.1.2 售票案例

**多线程编程步骤**

1. 创建资源类，在资源类创建属性和操作方法

2. 创建多个线程并调用资源类的操作方法

``` java
class Ticket {
    private int ticketCount = 30;

    public synchronized void sale() {
        if (ticketCount > 0){
            System.out.println("now ticketCount : " + --ticketCount + "\t" +
                    Thread.currentThread().getName() + "\t" + "have sailed one ticket");
        } else {
            System.out.println(Thread.currentThread().getName() + "\t" +
                    "want to sale one ticket, but all ticket had been sailed");
        }
    }
}

public class TicketSale {

    public static void main(String[] args) {

        Ticket ticket = new Ticket();

        new Thread(() -> {
            for (int i = 0; i < 15; i++) {
                ticket.sale();
                try {
                    Thread.sleep(300);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "AA").start();

        new Thread(() -> {
            for (int i = 0; i < 15; i++) {
                ticket.sale();
                try {
                    Thread.sleep(300);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "BB").start();

        new Thread(() -> {
            for (int i = 0; i < 15; i++) {
                ticket.sale();
                try {
                    Thread.sleep(300);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "CC").start();
    }
}
```

**这个案例是我设计的，因为sychronized关键字修饰的是非静态方法，所以监视器为对象，这里三个线程都调用同一个对象的sychronized方法，故会因为同步机制导致同一时间只有一个能执行，然后再进行睡眠 300ms**

**那么如果这个获取锁的线程由于要等待 IO 或者其他原因（比如调用 sleep方法）被阻塞了，但是又没有释放锁，其他线程便只能干巴巴地等待，试想一下，这多么影响程序执行效率。**

**因此就需要有一种机制可以不让等待的线程一直无期限地等待下去（比如只等待一定的时间或者能够响应中断），通过 Lock 就可以办到。**



## 2.2 什么是 Lock

Lock 锁实现提供了比使用同步方法和语句可以获得的更广泛的锁操作。它们允许更灵活的结构，可能具有非常不同的属性，并且可能支持多个关联的条件对象。Lock 提供了比 synchronized 更多的功能。

**Lock 与的 Synchronized 区别**

- Lock 不是 Java 语言内置的，synchronized 是 Java 语言的关键字，因此是内置特性。Lock 是一个类，通过这个类可以实现同步访问；

- Lock 和 synchronized 有一点非常大的不同，**采用 synchronized 不需要用户去手动释放锁**，当 synchronized 方法或者 synchronized 代码块执行完之后，系统会自动让线程释放对锁的占用；**而 Lock 则必须要用户去手动释放锁，如果没有主动释放锁，就有可能导致出现死锁现象**



### 2.2.1 Lock 接口

```java
public interface Lock {
    void lock();
    void lockInterruptibly() throws InterruptedException;
    boolean tryLock();
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
    void unlock();
    Condition newCondition();
}
```



### 2.2.2 lock

lock()方法是平常使用得最多的一个方法，就是用来获取锁。如果锁已被其他线程获取，则进行等待。

采用 Lock，必须主动去释放锁，并且在发生异常时，不会自动释放锁。因此一般来说，使用 Lock 必须在 try{}catch{}块中进行，并且将释放锁的操作放在 finally 块中进行，以保证锁一定被被释放，防止死锁的发生。通常使用 Lock来进行同步的话，是以下面这种形式去使用的：

```java
{
    // ---------------
	Lock lock = ...;
    try {
        lock.lock();
        //处理任务
    } catch(Exception ex){ 
        //处理异常
    } finally {
        lock.unlock(); //释放锁
    }
	// ----------------
}
```



### 2.2.3 newCondition

关键字 synchronized 与 wait()/notify()这两个方法一起使用可以实现等待/通知模式， Lock 锁的 newContition()方法返回 Condition 对象，Condition 类也可以实现等待/通知模式。

**用 notify()通知时，JVM 会随机唤醒某个等待的线程， 使用 Condition 类可以进行选择性通知** ， Condition 比较常用的两个方法：

- await()会使当前线程等待,同时会释放锁,当其他线程调用 signal()时,线程会重新获得锁并继续执行。

- signal()用于唤醒一个等待的线程。

**注意**：在调用 Condition 的 await()/signal()方法前，也需要线程持有相关的 Lock 锁，调用 await()后线程会释放这个锁，在 singal()调用后会从当前Condition 对象的等待队列中，唤醒 一个线程，唤醒的线程尝试获得锁， 一旦获得锁成功就继续执行。



## 2.3 ReentrantLock

ReentrantLock，意思是“可重入锁”，关于可重入锁的概念将在后面讲述。

ReentrantLock 是唯一实现了 Lock 接口的类，并且 ReentrantLock 提供了更多的方法。下面通过一些实例看具体看一下如何使用。

**可重入锁** 指的是同一个线程可无限次地进入同一把锁的不同代码，又因该锁通过线程独占共享资源的方式确保并发安全，又称为 **独占锁**。**可重入指的是已经获得该锁了，但在代码块里还能接着获得该锁，只是后面也要释放两次该锁。<font color='bb000'>下面这个例子并没有体现这一点，但zookeeper的笔记有一个帮助生成关于zookeeper锁的那里有代码，那个锁的案例是展示了多次上锁和对应次数释放锁的操作。</font>**

举个例子：同一个类中的 `synchronize` 关键字修饰了不同的方法。`synchronize` 是内置的隐式的可重入锁，例子中的两个方法使用的是同一把锁，只要能执行 `testB()` 也就说明线程拿到了锁，所以执行 `testA()` 方法就不用被阻塞等待获取锁了；如果不是同一把锁或非可重入锁，就会在执行`testA()` 时被阻塞等待。

**上面的例子换成使用lock接口来复现。**

**<font color='bb000'>补充一下，并不是调用start方法就代表线程立马被创建了，其实点击进入源码，可以看到当start执行的时候，需要先进行start0方法调用，这个方法是native方法，即是否创建由操作系统决定，而java代码层面无能为力。</font>**

```java
class LTicket {
    private int ticketCount = 30;

    // 创建可重入锁
    private final ReentrantLock lock = new ReentrantLock();

    public void sale() {
        // 上锁
        lock.lock();

        try {
            if (ticketCount > 0){
                System.out.println("now ticketCount : " + --ticketCount + "\t" +
                        Thread.currentThread().getName() + "\t" + "have sailed one ticket");
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            // 释放锁
            lock.unlock();
        }
    }
}

public class LTicketSale {

    public static void main(String[] args) {
        LTicket ticket = new LTicket();

        new Thread(() -> {
            for (int i = 0; i < 20; i++) {
                ticket.sale();
                try {
                    Thread.sleep(300);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "AA").start();

        new Thread(() -> {
            for (int i = 0; i < 20; i++) {
                ticket.sale();
                try {
                    Thread.sleep(300);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "BB").start();

        new Thread(() -> {
            for (int i = 0; i < 20; i++) {
                ticket.sale();
                try {
                    Thread.sleep(300);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "CC").start();
    }
}
```



## 2.4 小结(重点)

**<font color='000bb'>Lock 和 synchronized 有以下几点不同：</font>**

1. `Lock` 是一个 **接口**，而 `synchronized` 是 `Java` 中的 **关键字**，`synchronized` 是 **内置的语言实现;**

2. `synchronized` 在发生异常时，**会自动释放线程占有的锁，因此不会导致死锁现象发生** ；**而 `Lock` 在发生异常时，如果没有主动通过 `unLock()` 去释放锁，则很可能造成死锁现象** ，因此使用 `Lock` 时需要在 `finally` 块中释放锁；

3. **Lock 可以让等待锁的线程响应中断，而 synchronized 却不行，使用 synchronized 时，等待的线程会一直等待下去，不能够响应中断；如果 使用ReentrantLock，如果A不释放，可以使B在等待了足够长的时间以后，中断等待，而干别的事情**

4. **通过 Lock 可以知道有没有成功获取锁，而 synchronized 却无法办到。**
5. **Lock 可以提高多个线程进行读操作的效率。在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而当竞争资源非常激烈时（即有大量线程同时竞争），此时 `Lock` 的性能要远远优于 `synchronized`。**
6. **通过 lock 接口的实现类的 `newCondition()`，可以完成精准线程的唤醒，`synchronized` 不可以做到，只能通过每个对象自带的 `notify` 和 `wait` 做到**



# 3 线程间通信 

线程间通信的模型有两种：共享内存和消息传递，以下方式都是基本这两种模型来实现的。我们来基本一道面试常见的题目来分析

**场景---两个线程，一个线程对当前数值加 1，另一个线程对当前数值减 1,要求用线程间通信**

![5.png](./1 JUC基础篇.assets/b-5.png)

**<font color='bb000'>同时这里要补充一点，我们在进入线程通信的等待唤醒的条件判断，应该使用 `while`，这样能保证不会出现虚假唤醒，因为java的唤醒机制是，从哪里睡眠，就从哪里唤醒，如果使用`if`导致出现并不应该唤醒的情况下，线程被唤醒，错误的执行了不该执行的方法。</font>**



## 3.1 synchronized 方案

```java
class share {
    // 资源
    private int numbers = 0;

    // +1 的方法
    public synchronized void incr() throws InterruptedException {
        // 第二步 判断 干活 通知
        while (numbers == 1) {
            this.wait();
        }
        // 如果 number 值为 0 就 + 1
        numbers++;
        this.notifyAll();
        System.out.println(Thread.currentThread().getName() + " :: " + numbers);
    }

    // -1 的方法
    public synchronized void decr() throws InterruptedException {
        while (numbers != 1) {
            this.wait();
        }
        numbers--;
        this.notifyAll();
        System.out.println(Thread.currentThread().getName() + " :: " + numbers);
    }
}

public class ThreadDemo1 {

    public static void main(String[] args) {
        share share = new share();

        new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    share.incr();
                }
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }, "AA").start();

        new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    share.decr();
                }
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }, "BB").start();
        
                new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    share.incr();
                }
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }, "CC").start();

        new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    share.decr();
                }
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }, "DD").start();
    }
}
```



## 3.2 Lock 方案 

**<font color='bb000'>切记使用 Lock 接口，一定要使用 try finally 环绕，防止出现异常之后导致死锁。</font>**

```java
class Share {

    private int numbers = 0;
    private final Lock lock = new ReentrantLock();

    private Condition condition = lock.newCondition();

    // + 1 方法
    public void incr() throws InterruptedException {

        try {
            lock.lock();
            while (numbers == 1){
                condition.await();
            }
            numbers ++;
            System.out.println(Thread.currentThread().getName() + " :: " + numbers);
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }

    // - 1 方法
    public void decr() throws InterruptedException {
        try {
            lock.lock();
            while (numbers != 1){
                condition.await();
            }
            numbers --;
            System.out.println(Thread.currentThread().getName() + " :: " + numbers);
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }

}

public class ThreadDemo2 {

    public static void main(String[] args) {
        Share share = new Share();

        new Thread(()-> {
            try {
                for (int i = 0; i < 10; i++) {
                    share.incr();
                }
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }, "AA").start();

        new Thread(()-> {
            try {
                for (int i = 0; i < 10; i++) {
                    share.decr();
                }
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }, "BB").start();

        new Thread(()-> {
            try {
                for (int i = 0; i < 10; i++) {
                    share.incr();
                }
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }, "CC").start();

        new Thread(()-> {
            try {
                for (int i = 0; i < 10; i++) {
                    share.decr();
                }
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }, "DD").start();
    }
}
```



## 3.3 线程间定制化通信 

### 3.3.1 案例介绍 

**问题: A 线程打印 5 次 A，B 线程打印 10 次 B，C 线程打印 15 次 C,按照此顺序循环 10 轮**

**<font color='bb000'>这里定义 flag 是为了线程阻塞的时候，能按情况进行阻塞，如果flag不到达合法值就一直等待被唤醒，而为什么设置三个`condition`，是因为唤醒的时候就不用使用 `signalAll`了，要不然还要全部唤醒，进入二次竞争，然后再次等待，每进行一次唤醒，就进行一次竞争行为，效率降低。</font>**

```java
class ThreadShare {
    private Lock lock = new ReentrantLock();
    private Condition c1 = lock.newCondition();
    private Condition c2 = lock.newCondition();
    private Condition c3 = lock.newCondition();
    private int flag = 0;

    public void print5(int loop) throws InterruptedException {
        try {
            lock.lock();
            while (flag != 0) {
                c1.await();
            }
            for (int i = 0; i < 5; i++) {
                System.out.println(Thread.currentThread().getName() + " :: " + i + " 轮数 : " + loop + " flag : " + flag);
            }
            flag = (flag + 1) % 3;
            c2.signal();
        } finally {
            lock.unlock();
        }
    }

    public void print10(int loop) throws InterruptedException {
        try {
            lock.lock();
            while (flag != 1) {
                c2.await();
            }
            for (int i = 0; i < 10; i++) {
                System.out.println(Thread.currentThread().getName() + " :: " + i + " 轮数 : " + loop);
            }
            flag = (flag + 1) % 3;
            c3.signal();
        } finally {
            lock.unlock();
        }
    }

    public void print15(int loop) throws InterruptedException {
        try {
            lock.lock();
            while (flag != 2) {
                c3.await();
            }
            for (int i = 0; i < 15; i++) {
                System.out.println(Thread.currentThread().getName() + " :: " + i);
            }
            flag = (flag + 1) % 3;
            c1.signal();
        } finally {
            lock.unlock();
        }
    }
}

public class ThreadDemo3 {

    public static void main(String[] args) {
        ThreadShare share = new ThreadShare();

        new Thread(()->{
            for (int i = 0; i < 5; i++) {
                try {
                    share.print5(i);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "AA").start();

        new Thread(()->{
            for (int i = 0; i < 5; i++) {
                try {
                    share.print10(i);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "BB").start();

        new Thread(()->{
            for (int i = 0; i < 5; i++) {
                try {
                    share.print15(i);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "CC").start();
    }
}
```

**synchronized实现同步的基础:Java中的每一个对象都可以作为锁。具体表现为以下3种形式。**

**对于普通同步方法，锁是当前实例对象。**

**对于静态同步方法，锁是当前类的class对象。**

**对于同步方法块，锁是synchonized括号里配置的对象**



# 4 集合的线程安全 

## 4.1 集合操作 Demo 

**创建30个线程，每个线程都分别执行三次插入操作，线程一旦开始就会并发的和前面未执行过三次的线程出现并发书写的线程写异常。**

```java
public class ThreadDemo4 {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();

        for (int i = 0; i < 10; i++) {
            // 创建10个线程 均向集合添加内容
            for (int j = 0; j < 3; j++) {
                new Thread(() -> {
                    list.add(UUID.randomUUID().toString().substring(0, 8));
                    System.out.println(list);
                }, String.valueOf(i)).start();
            }
        }
    }
}
```

`java.util.ConcurrentModificationException`

**问题: 为什么会出现并发修改异常?**

查看 ArrayList 的 add 方法源码

**没有synchronized关键字**

```java
/**
* Appends the specified element to the end of this list.
*
* @param e element to be appended to this list
* @return <tt>true</tt> (as specified by {@link Collection#add})
*/
public boolean add(E e) {
ensureCapacityInternal(size + 1); // Increments modCount!!
elementData[size++] = e;
return true;
}
```

**那么我们如何去解决 List 类型的线程安全问题?**



## 4.2 Vector 

Vector 是**矢量队列**，它是 JDK1.0 版本添加的类。继承于 AbstractList，实现了 List, RandomAccess, Cloneable 这些接口。 Vector 继承了 AbstractList，实现了 List；所以，**它是一个队列，支持相关的添加、删除、修改、遍历等功能**。 Vector 实现了 RandomAccess 接口，即**提供了随机访问功能**。

RandomAccess 是 java 中用来被 List 实现，为 List 提供快速访问功能的。在Vector 中，我们即可以通过元素的序号快速获取元素对象；这就是快速随机访问。 Vector 实现了 Cloneable 接口，即实现 clone()函数。它能被克隆。

**==和 ArrayList 不同，Vector 中的操作是线程安全的。==**

代码修改

```java
public class ThreadDemo4 {
    public static void main(String[] args) {
        List<String> list = new Vector<>();

        for (int i = 0; i < 30; i++) {
            // 创建10个线程 均向集合添加内容
            for (int j = 0; j < 3; j++) {
                new Thread(() -> {
                    list.add(UUID.randomUUID().toString().substring(0, 8));
                    System.out.println(list);
                }, String.valueOf(i)).start();
            }
        }
    }
}
```



## 4.3 Collections 

Collections 提供了方法 synchronizedList 保证 list 是同步线程安全的

```java
public class ThreadDemo4 {
    public static void main(String[] args) {
        //List<String> list = new ArrayList<String>();
        //List<String> list = new Vector<>();
        List<String> list = Collections.synchronizedList(new ArrayList<String>());

        for (int i = 0; i < 30; i++) {
            // 创建10个线程 均向集合添加内容
            for (int j = 0; j < 3; j++) {
                new Thread(() -> {
                    list.add(UUID.randomUUID().toString().substring(0, 8));
                    System.out.println(list);
                }, String.valueOf(i)).start();
            }
        }
    }
}
```

**源码**

```java
public static <T> List<T> synchronizedList(List<T> list) {
    return (list instanceof RandomAccess ?
        new SynchronizedRandomAccessList<>(list) : new SynchronizedList<>(list));
}
```



## 4.4 CopyOnWriteArrayList(重点) 

首先我们对 `CopyOnWriteArrayList` 进行学习,其特点如下:

它相当于线程安全的 `ArrayList`。和 `ArrayList` 一样，它是个可变数组；但是和ArrayList 不同的时，它具有以下特性：

1. 它最适合于具有以下特征的应用程序：`List` 大小通常保持很小，只读操作远多于可变操作，需要在遍历期间防止线程间的冲突。

2. 它是线程安全的。
3. 因为通常需要复制整个基础数组，所以可变操作（`add()`、`set()` 和 `remove()` 等等）的开销很大。

4. 迭代器支持 `hasNext()`, `next()` 等不可变操作，但不支持可变 `remove()` 等操作。
5. 使用迭代器进行遍历的速度很快，并且不会与其他线程发生冲突。在构造迭代器时，迭代器依赖于不变的数组快照。

**1. 独占锁效率低：采用读写分离思想解决**

**2. 写线程获取到锁，其他写线程阻塞**

**3. 复制思想：**

当我们往一个容器添加元素的时候，不直接往当前容器添加，而是先将当前容器进行 Copy，复制出一个新的容器，然后新的容器里添加元素，添加完元素之后，再将原容器的引用指向新的容器。

**这时候会抛出来一个新的问题，也就是数据不一致的问题。如果写线程还没来得及写会内存，其他的线程就会读到了脏数据。**

==**这就是 CopyOnWriteArrayList 的思想和原理。就是拷贝一份。**==

没有线程安全问题

**原因分析**(**重点**):==**动态数组与线程安全**==

下面从“动态数组”和“线程安全”两个方面进一步对CopyOnWriteArrayList 的原理进行说明。

- **“动态数组”机制**

- 它内部有个“volatile 数组”(array)来保持数据。在“添加/修改/删除”数据时，都会新建一个数组，并将更新后的数据拷贝到新建的数组中，最后再将该数组赋值给“volatile 数组”, 这就是它叫做 CopyOnWriteArrayList 的原因

- **由于它在“添加/修改/删除”数据时，都会新建数组，所以涉及到修改数据的操作，CopyOnWriteArrayList 效率很低；但是单单只是进行遍历查找的话，效率比较高。**

- **“线程安全”机制**
- 通过 `volatile` 和互斥锁来实现的。

- 通过“ `volatile` 数组”来保存数据的。一个线程读取 `volatile` 数组时，总能看到其它线程对该 `volatile` 变量最后的写入；就这样，通过 `volatile` 提供了“读取到的数据总是最新的”这个机制的保证。

- 通过互斥锁来保护数据。在“添加/修改/删除”数据时，会先“获取互斥锁”，再修改完毕之后，先将数据更新到“`volatile` 数组”中，然后再“释放互斥锁”，就达到了保护数据的目的。



## 4.5 小结(重点) 

**1.线程安全与线程不安全集合**

集合类型中存在线程安全与线程不安全的两种,常见例如:

`ArrayList` ----- `Vector`

`HashMap`  ----- `HashTable`

但是以上都是通过 `synchronized` 关键字实现,效率较低

**2.Collections 构建的线程安全集合**

**3.java.util.concurrent 并发包下**

`CopyOnWriteArrayList` `CopyOnWriteArraySet` `ConcurrentHashMap` 类型,通过动态数组与线程安全个方面保证线程安全



# 5 多线程锁 

## 5.1 锁的八个问题演示 

```java
class Phone {
    public static synchronized void sendSMS() throws Exception {
        //停留 4 秒
        TimeUnit.SECONDS.sleep(4);
        System.out.println("------sendSMS");
    }

    public synchronized void sendEmail() throws Exception {
        System.out.println("------sendEmail");
    }

    public void getHello() {
        System.out.println("------getHello");
    }
}
```

**1 标准访问，先打印短信还是邮件**

------sendSMS

------sendEmail

**这里把睡4秒注释，因为两个线程创建之间主线程睡了100ms，导致必定第一个线程先创建，故必然执行顺序固定为上**

```java
    public static void main(String[] args) throws Exception {
        Phone phone1 = new Phone();
        Phone phone2 = new Phone();

        new Thread(()->{

            try {
                phone1.sendSMS();
            } catch (Exception e) {
                throw new RuntimeException(e);
            }

        }, "AA").start();

        Thread.sleep(100);

        new Thread(()->{

            try {
                phone1.sendEmail();
            } catch (Exception e) {
                throw new RuntimeException(e);
            }

        }, "BB").start();
    }
```

**2 停 4 秒在短信方法内，先打印短信还是邮件**

------sendSMS

------sendEmail

**如果把睡4秒取消注释，还是这个顺序，因为我们的锁是加在非静态方法，即给对象加锁，而调用同一个对象，故当第一个线程执行senSMS，持有的是类锁，第二个线程可以获取对象锁，执行其他的同步方法。**

**3 新增普通的 hello 方法，是先打短信还是 hello**

------getHello

------sendSMS

**显然，肯定是非同步方法执行，它不是同步方法，不需要拿锁，不会被阻塞**

**4 现在有两部手机，先打印短信还是邮件**

------sendEmail

------sendSMS

**必然没有睡的sendEmail先执行完，目前是对象锁，换了对象，拿到的不是同一个锁，那加了线程休眠的肯定执行的慢**

**5 两个静态同步方法，1 部手机，先打印短信还是邮件**

------sendSMS

------sendEmail

**6 两个静态同步方法，2 部手机，先打印短信还是邮件**

------sendSMS

------sendEmail

**既然是静态方法，那就是单例的Class类锁，那显然执行的方法哪怕对象不同，仍会阻塞别的Class锁的线程。**

**7          1 个静态同步方法,1 个普通同步方法，1 部手机，先打印短信还是邮件**

------sendEmail

------sendSMS

**8         1 个静态同步方法,1 个普通同步方法，2 部手机，先打印短信还是邮件**

------sendEmail

------sendSMS

**一个是Class锁，一个是对象锁，互不影响，所以不睡的方法，显然先执行。**

**结论 : **

一个对象里面如果有多个 `synchronized` 方法，某一个时刻内，只要一个线程去调用其中的一个 `synchronized` 方法【`前提是监视器相同`】了，其它的线程都只能等待，换句话说，某一个时刻内，只能有唯一一个线程去访问这些 `synchronized` 方法锁的是当前对象 `this`，被锁定后，其它的线程都不能进入到当前对象的其它的synchronized 方法

加个普通方法后发现和同步锁无关

换成两个对象后，不是同一把锁了，情况立刻变化。

`synchronized` 实现同步的基础：`Java` 中的每一个对象都可以作为锁。

**具体表现为以下** **3** **种形式。**

**对于普通同步方法，锁是当前实例对象。**

**对于静态同步方法，锁是当前类的** **Class** **对象。**

**对于同步方法块，锁是** **Synchonized** **括号里配置的对象**

当一个线程试图访问同步代码块时，它首先必须得到锁，退出或抛出异常时必须释放锁。也就是说如果一个实例对象的非静态同步方法获取锁后，该实例对象的其他非静态同步方法必须等待获取锁的方法释放锁后才能获取锁，可是别的实例对象的非静态同步方法因为跟该实例对象的非静态同步方法用的是不同的锁，所以毋须等待该实例对象已获取锁的非静态同步方法释放锁就可以获取他们自己的锁。

所有的静态同步方法用的也是同一把锁——类对象本身，这两把锁是两个不同的对象，所以静态同步方法与非静态同步方法之间是不会有竞争条件的。

但是一旦一个静态同步方法获取锁后，其他的静态同步方法都必须等待该方法释放锁后才能获取锁，而不管是同一个实例对象的静态同步方法之间，还是不同的实例对象的静态同步方法之间，只要它们同一个类的实例对象，就会产生竞争关系！



## 5.2 公平锁和非公平锁

![6.png](./1 JUC基础篇.assets/b-6.png)

**这里以之前的卖票代码为案例，我们发现 ReentrantLock 默认构造就是非公平锁，同时有一个带布尔的有参构造是可以构建公平锁。**

之前卖票案例，可以看到在AA线程没有结束for循环之前，其他线程没有机会调用方法进行卖票。

**非公平锁 ： 线程饿死      效率高**

**公平锁 ： 均衡调用      效率低**

**非公平锁的话就是抢的，就不管你来的早还是晚，同时竞争，公平锁的话就是有一个队列，咱们依次按队列排。**

`

## 5.3. 可重入锁

**synchronized（隐式）和Lock（显式）都是可重入锁**

**重复获取锁有一个加锁计数器，同一个线程获取同一个锁时+1，离开时-1，到0就完全释放锁**

**可重入锁**指的是同一个线程可无限次地进入同一把锁的不同代码，又因该锁通过线程独占共享资源的方式确保并发安全，又称为**独占锁**。**可重入指的是已经获得该锁了，但在代码块里还能接着获得该锁，只是后面也要释放两次该锁。**

举个例子：同一个类中的synchronize关键字修饰了不同的方法。synchronize是内置的隐式的可重入锁，例子中的两个方法使用的是同一把锁，只要能执行testB()也就说明线程拿到了锁，所以执行testA()方法就不用被阻塞等待获取锁了；如果不是同一把锁或非可重入锁，就会在执行testA()时被阻塞等待。

```java
class tryReentrant{

    public synchronized void testA() throws InterruptedException {
        System.out.println( Thread.currentThread().getName() + " :: TestA");
        Thread.sleep(1000);
        testB();
    }

    public synchronized void testB(){
        System.out.println(Thread.currentThread().getName() + " :: TestB");
    }
}

public class ReentrantLockTest {

    public static void main(String[] args) throws InterruptedException {
        tryReentrant tryReentrant = new tryReentrant();

        new Thread(()->{
            try {
                tryReentrant.testA();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }, "AA").start();

        Thread.sleep(100);

        new Thread(tryReentrant::testB, "BB").start();
    }
}
```

```sh
AA :: TestA
AA :: TestB
BB :: TestB
```



## 5.4. 死锁

**不同的线程分别占用对方需要的同步资源不放弃，都在等待对方放弃自己需要的同步资源，就形成了线程的死锁。 一旦出现死锁，整个程序既不会发生异常，也不会给出任何提示，只是所有线程处于阻塞状态，无法继续。**

![4.png](./1 JUC基础篇.assets/b-4.png)

**诱发死锁的原因：**

- **互斥条件**
- **占用且等待**
- **不可抢夺（或不可抢占）**
- **循环等待**

**以上4个条件，同时出现就会触发死锁。**

**解决死锁：**

死锁一旦出现，基本很难人为干预，只能尽量规避。可以考虑打破上面的诱发条件。

**针对条件1：互斥条件基本上无法被破坏。因为线程需要通过互斥解决安全问题。**

**针对条件2：可以考虑一次性申请所有所需的资源，这样就不存在等待的问题。**

**针对条件3：占用部分资源的线程在进一步申请其他资源时，如果申请不到，就主动释放掉已经占用的资源。**

**针对条件4：可以将资源改为线性顺序。申请资源时，先申请序号较小的，这样避免循环等待问题。**

```java
public class DeadLock {

    public static void main(String[] args) {
        Object a = new Object();
        Object b = new Object();

        new Thread(()-> {
            synchronized (a){
                System.out.println(Thread.currentThread().getName() + 
                                   " 持有锁 a 想要持有锁 b");
                try {
                    Thread.sleep(200);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
                synchronized (b){
                    System.out.println(Thread.currentThread().getName() +
                                       " 持有锁 a b 线程死亡");
                }
            }
        }, "A").start();

        new Thread(()-> {
            synchronized (b){
                System.out.println(Thread.currentThread().getName() + 
                                   " 持有锁 b 想要持有锁 a");
                try {
                    Thread.sleep(200);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
                synchronized (a){
                    System.out.println(Thread.currentThread().getName() + 
                                       " 持有锁 a b 线程死亡");
                }
            }
        }, "B").start();
    }
}
```

**验证是否死锁**

**1 jps** 

**如果没有给 jps.exe 配置到环境变量需要自行前往jdk目录使用命令**

```sh
jps -l
```

![7.png](./1 JUC基础篇.assets/b-7.png)

**2 jstack**

```sh
jstack 23604
```

![8.png](./1 JUC基础篇.assets/b-8.png)



# 6 Callable & Future 接口

## 6.1 Callable 接口

目前我们学习了有两种创建线程的方法-一种是通过创建 Thread 类，另一种是通过使用 Runnable 创建线程。但是，Runnable 缺少的一项功能是，当线程终止时（即 run（）完成时），我们无法使线程返回结果。为了支持此功能，Java 中提供了 Callable 接口。

==**现在我们学习的是创建线程的第三种方案---Callable 接口**==

**Callable 接口的特点如下(重点)**

- 为了实现 Runnable，需要实现不返回任何内容的 run（）方法，而对于Callable，需要实现在完成时返回结果的 call（）方法。

- call（）方法可以引发异常，而 run（）则不能。

- 为实现 Callable 而必须重写 call 方法。

- 不能直接替换 runnable,因为 Thread 类的构造方法根本没有 Callable。



## 6.2 Future 接口 

当 call（）方法完成时，结果必须存储在主线程已知的对象中，以便主线程可以知道该线程返回的结果。为此，可以使用 Future 对象。

将 Future 视为保存结果的对象–它可能暂时不保存结果，但将来会保存（一旦 Callable 返回）。Future 基本上 **是主线程可以跟踪进度以及其他线程的结果的一种方式。** 要实现此接口，必须重写 5 种方法，这里列出了重要的方法,如下:

- **public boolean cancel（boolean mayInterrupt）：** 用于停止任务。

==如果尚未启动，它将停止任务。如果已启动，则仅在 mayInterrupt 为 true 时才会中断任务。==

- **public Object get（）throws InterruptedException，ExecutionException：** 用于获取任务的结果。

==如果任务完成，它将立即返回结果，否则将等待任务完成，然后返回结果。==

- **public boolean isDone（）：** 如果任务完成，则返回 true，否则返回 false可以看到 Callable 和 Future 做两件事 - Callable 与 Runnable 类似，因为它封装了要在另一个线程上运行的任务，而 Future 用于存储从另一个线程获得的结果。实际上，future 也可以与 Runnable 一起使用。

要创建线程，需要 `Runnable`。为了获得结果，需要 `future`。



## 6.3 FutureTask 

Java 库具有具体的 FutureTask 类型，该类型实现 Runnable 和 Future，并方便地将两种功能组合在一起。 可以通过为其构造函数提供 Callable 来创建FutureTask。然后，将 FutureTask 对象提供给 Thread 的构造函数以创建 Thread 对象。因此，间接地使用 Callable 创建线程。

**核心原理:(重点)**

在主线程中需要执行比较耗时的操作时，但又不想阻塞主线程时，可以把这些作业交给 Future 对象在后台完成

- 当主线程将来需要时，就可以通过 Future 对象获得后台作业的计算结果或者执行状态

- 一般 FutureTask 多用于耗时的计算，主线程可以在完成自己的任务后，再去获取结果。

- 仅在计算完成时才能检索结果；如果计算尚未完成，则阻塞 get 方法

- 一旦计算完成，就不能再重新开始或取消计算

- get 方法而获取结果只有在计算完成时获取，否则会一直阻塞直到任务转入完成状态，然后会返回结果或者抛出异常

- get 只计算一次,因此 get 方法放到最后

```java
class Thread1 implements Runnable {
    @Override
    public void run() {
		
    }
}

// 实现Callable接口
class Thread2 implements Callable {
    @Override
    public Integer call() throws Exception {
        return 200;
    }
}


public class Demo1 {
    public static void main(String[] args) {
        // Runnable 接口线程
        new Thread(new Thread1(), "AA").start();

        // Callable 接口线程 报错
        // new Thread(new Thread2(), "BB").start();

        // FutureTask
        FutureTask<Integer> futureTask1 = new FutureTask<>(new Thread2());

        // lambda
        FutureTask<Integer> futureTask2 = new FutureTask<>(() -> 1024);
    }
}
```



## 6.4 使用 Callable 和 Future 

**改写上面的main方法**

```java
// 实现Callable接口
class Thread2 implements Callable {
    @Override
    public Integer call() throws Exception {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + " is sleeping :: idx is :: " + i);
            Thread.sleep(1000);
        }
        return 200;
    }
}

public class Demo1 {
    public static void main(String[] args) throws InterruptedException {

        // FutureTask
        FutureTask<Integer> futureTask1 = new FutureTask<>(new Thread2());

        // lambda
        FutureTask<Integer> futureTask2 = new FutureTask<>(() -> {
            while (!futureTask1.isDone()){
                System.out.println(Thread.currentThread().getName() + " is waiting...");
                Thread.sleep(500);
            }
            System.out.println(Thread.currentThread().getName() + " get futureTask1 :: " + futureTask1.get());
            return 1024;
        });

        new Thread(futureTask1, "AA").start();
        Thread.sleep(300);
        new Thread(futureTask2, "BB").start();
    }
}
```

**这里通过AA BB 线程之间的主线程睡眠保证 AA 线程先创建， 然后 AA 线程睡眠 5 秒才会返回值，而 BB 线程每 500ms 就会尝试通过 futureTask1的get方法获取线程1的 futureTask1 获取返回值，但因为AA线程的run方法没有执行完而阻塞。**

```sh
AA is sleeping :: idx is :: 0
BB is waiting...
BB is waiting...
AA is sleeping :: idx is :: 1
BB is waiting...
BB is waiting...
AA is sleeping :: idx is :: 2
BB is waiting...
BB is waiting...
AA is sleeping :: idx is :: 3
BB is waiting...
BB is waiting...
AA is sleeping :: idx is :: 4
BB is waiting...
BB is waiting...
BB get futureTask1 :: 200
```



## 6.5 小结(重点) 

- 在主线程中需要执行比较耗时的操作时，但又不想阻塞主线程时，可以把这些作业交给 Future 对象在后台完成, 当主线程将来需要时，就可以通过 Future 对象获得后台作业的计算结果或者执行状态。

- 一般 FutureTask 多用于耗时的计算，主线程可以在完成自己的任务后，再去获取结果。

- 仅在计算完成时才能检索结果；如果计算尚未完成，则阻塞 get 方法。一旦计算完成，就不能再重新开始或取消计算。get 方法而获取结果只有在计算完成时获取，否则会一直阻塞直到任务转入完成状态，然后会返回结果或者抛出异常。

- 只计算一次。



# 7 JUC 三大辅助类

JUC 中提供了三种常用的辅助类，通过这些辅助类可以很好的解决线程数量过多时 Lock 锁的频繁操作。这三种辅助类为：

- **CountDownLatch: 减少计数**

- **CyclicBarrier: 循环栅栏**
- **Semaphore: 信号灯**

下面我们分别进行详细的介绍和学习



## 7.1 减少计数 CountDownLatch

CountDownLatch 类可以设置一个计数器，然后通过 countDown 方法来进行减 1 的操作，使用 await 方法等待计数器不大于 0，然后继续执行 await 方法之后的语句。

- CountDownLatch 主要有两个方法，当一个或多个线程调用 await 方法时，这些线程会阻塞

- 其它线程调用 countDown 方法会将计数器减 1(调用 countDown 方法的线程不会阻塞)

- 当计数器的值变为 0 时，因 await 方法阻塞的线程会被唤醒，继续执行

**场景: 6 个用户线程陆续离开后主线程关闭。**

```java
public class CountDownDemo {

    public static void main(String[] args) throws InterruptedException {

        CountDownLatch countDownLatch = new CountDownLatch(6);
        System.out.println("主线程要关闭 关闭前请其他线程陆续退出");
        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName() + " :: is exiting");
                countDownLatch.countDown();
            }, "线程" + String.valueOf(i)).start();
        }

        countDownLatch.await();
        System.out.println(Thread.currentThread().getName() + " is finished");
    }
}
```



## 7.2 循环栅栏 CyclicBarrier

CyclicBarrier 看英文单词可以看出大概就是循环阻塞的意思，在使用中CyclicBarrier 的构造方法第一个参数是目标障碍数，每次执行 CyclicBarrier 一次障碍数会加一，如果达到了目标障碍数，才会执行 cyclicBarrier.await()之后的语句。可以将 CyclicBarrier 理解为加 1 操作

**跟cyclicbarrier await线程数相关 只要await达到的线程数刚好符合 就唤醒所有线程并执行runnable方法**

**场景: 集齐 7 颗龙珠就可以召唤神龙**

```java
public class CyclicBarrierDemo {
    
    private static int NUM = 7;
    
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(NUM, ()->{
            System.out.println("七颗龙珠被收集到了 可以召唤神龙!");
        });

        for (int i = 1; i <= 7; i++) {
            new Thread(()-> {
                try {
                    System.out.println(Thread.currentThread().getName() + " :: 星龙珠被收集到了!");
                    cyclicBarrier.await();
                } catch (Exception e) {
                    throw new RuntimeException(e);
                }
            }, String.valueOf(i)).start();
        }
    }
}
```



## 7.3 信号灯 Semaphore

`Semaphore` 的构造方法中传入的第一个参数是最大信号量（可以看成最大线程池），每个信号量初始化为一个最多只能分发一个许可证。使用 `acquire` 方法获得许可证，`release` 方法释放许可

**场景: 抢车位, 6 部汽车 3 个停车位**

```java
public class SemaphoreDemo {

    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3);

        for (int i = 1; i <= 6; i++) {
            new Thread(()-> {
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "抢到了车位....");
                    TimeUnit.SECONDS.sleep(new Random().nextInt(5));
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                } finally {
                    System.out.println(Thread.currentThread().getName() + "离开了车位....");
                    semaphore.release();
                }
            }, "线程 :: " + String.valueOf(i)).start();
        }
    }
}
```



# 8 读写锁

<img src="./1 JUC基础篇.assets/b-9.png" alt="9.png" style="zoom:67%;" />



## 8.1 读写锁介绍

现实中有这样一种场景：对共享资源有读和写的操作，且写操作没有读操作那么频繁。在没有写操作的时候，多个线程同时读一个资源没有任何问题，所以应该允许多个线程同时读取共享资源；但是如果一个线程想去写这些共享资源，就不应该允许其他线程对该资源进行读和写的操作了。

针对这种场景，**JAVA 的并发包提供了读写锁 ReentrantReadWriteLock，它表示两个锁，一个是读操作相关的锁，称为共享锁；一个是写相关的锁，称为排他锁**

1. **线程进入读锁的前提条件：**

- 没有其他线程的写锁

- 没有写请求, 或者==有写请求，但调用线程和持有锁的线程是同一个(可重入锁)。==

2. **线程进入写锁的前提条件：**

- 没有其他线程的读锁

- 没有其他线程的写锁

**而读写锁有以下三个重要的特性：**

（1）公平选择性：支持非公平（默认）和公平的锁获取方式，吞吐量还是非公平优于公平。

（2）重进入：读锁和写锁都支持线程重进入。

（3）锁降级：遵循获取写锁、获取读锁再释放写锁的次序，写锁能够降级成为读锁。



## 8.2 ReentrantReadWriteLock 

ReentrantReadWriteLock 类的整体结构

```java
public class ReentrantReadWriteLock implements ReadWriteLock, 
java.io.Serializable {
     /** 读锁 */
     private final ReentrantReadWriteLock.ReadLock readerLock;
     /** 写锁 */
     private final ReentrantReadWriteLock.WriteLock writerLock;
     final Sync sync;

     /** 使用默认（非公平）的排序属性创建一个新的
    ReentrantReadWriteLock */
     public ReentrantReadWriteLock() {
     	this(false);
     }
     /** 使用给定的公平策略创建一个新的 ReentrantReadWriteLock */
     public ReentrantReadWriteLock(boolean fair) {
         sync = fair ? new FairSync() : new NonfairSync();
         readerLock = new ReadLock(this);
         writerLock = new WriteLock(this);
     }
     /** 返回用于写入操作的锁 */
     public ReentrantReadWriteLock.WriteLock writeLock() { return     writerLock; }

     /** 返回用于读取操作的锁 */
     public ReentrantReadWriteLock.ReadLock readLock() { return readerLock; }
    
     abstract static class Sync extends AbstractQueuedSynchronizer {}
     static final class NonfairSync extends Sync {}
     static final class FairSync extends Sync {}
     public static class ReadLock implements Lock, java.io.Serializable {}
     public static class WriteLock implements Lock, java.io.Serializable {}
}         
```

可以看到，ReentrantReadWriteLock 实现了 ReadWriteLock 接口，ReadWriteLock 接口定义了获取读锁和写锁的规范，具体需要实现类去实现；同时其还实现了 Serializable 接口，表示可以进行序列化，在源代码中可以看到 ReentrantReadWriteLock 实现了自己的序列化逻辑。



## 8.3 入门案例 

**场景: 使用 ReentrantReadWriteLock 对一个 hashmap 进行读和写操作**

### 8.3.1 实现案例

```java
class Cache {

    private final HashMap<String, Object> map = new HashMap<>();
    private ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();

    public void put(String key, Object value) {

        try {
            System.out.println(Thread.currentThread().getName() + " is writing....");
            TimeUnit.SECONDS.sleep(1);
            map.put(key, value);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }

    public Object get(String key) {
        try {
            System.out.println(Thread.currentThread().getName() + " is reading....");
            TimeUnit.SECONDS.sleep(1);
            return map.get(key);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }

}

public class ReadWriteLockDemo {

    public static void main(String[] args) {
        Cache cache = new Cache();

        for (int i = 1; i <= 5; i++) {
            String num = "num::" + String.valueOf(i);
            new Thread(() -> {
                cache.put(num, num);
            }, "Thread::" + String.valueOf(i)).start();
        }

        for (int i = 1; i <= 5; i++) {
            String num = "num::" + String.valueOf(i);
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + " get value : " + cache.get(num));
            }, "Thread::" + String.valueOf(i)).start();
        }
    }
}
```

**此时不加读写锁，发现写的同时读操作也会进行，导致得到了null值。**

```java
Thread::2 is writing....
Thread::5 is reading....
Thread::1 is reading....
Thread::3 is reading....
Thread::2 is reading....
Thread::4 is writing....
Thread::5 is writing....
Thread::4 is reading....
Thread::1 is writing....
Thread::3 is writing....
Thread::1 get value : null
Thread::3 get value : null
Thread::5 get value : null
Thread::4 get value : null
Thread::2 get value : num::2
```

**如果此时加读写锁，就能避免如上现象 **

```java
class Cache {

    private final HashMap<String, Object> map = new HashMap<>();
    private ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();

    public void put(String key, Object value) {
        try {
            rwLock.writeLock().lock();
            System.out.println(Thread.currentThread().getName() + " is writing....");
            TimeUnit.SECONDS.sleep(1);
            map.put(key, value);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {
            rwLock.writeLock().unlock();
        }
    }

    public Object get(String key) {
        try {
            rwLock.readLock().lock();
            System.out.println(Thread.currentThread().getName() + " is reading....");
            TimeUnit.SECONDS.sleep(1);
            return map.get(key);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {
            rwLock.readLock().unlock();
        }
    }
}

public class ReadWriteLockDemo {

    public static void main(String[] args) {
        Cache cache = new Cache();

        for (int i = 1; i <= 5; i++) {
            String num = "num::" + String.valueOf(i);
            new Thread(() -> {
                cache.put(num, num);
            }, "Thread::" + String.valueOf(i)).start();
        }

        for (int i = 1; i <= 5; i++) {
            String num = "num::" + String.valueOf(i);
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + " get value : " + cache.get(num));
            }, "Thread::" + String.valueOf(i)).start();
        }
    }
}
```

**此时可以看到，加了写锁之后，此时写锁是排他锁，读的线程在写线程没写完前都无法读到，而写完后，读锁是共享锁，如果不加线程睡眠，会同时读出。**

```java
Thread::1 is writing....
Thread::2 is writing....
Thread::3 is writing....
Thread::4 is writing....
Thread::5 is writing....
Thread::1 is reading....
Thread::2 is reading....
Thread::3 is reading....
Thread::4 is reading....
Thread::5 is reading....
Thread::1 get value : num::1
Thread::5 get value : num::5
Thread::4 get value : num::4
Thread::3 get value : num::3
Thread::2 get value : num::2
```



## 8.4 小结(重要) 

- 在线程持有读锁的情况下，该线程不能取得写锁(因为获取写锁的时候，如果发现当前的读锁被占用，就马上获取失败，不管读锁是不是被当前线程持有)。

- 在线程持有写锁的情况下，该线程可以继续获取读锁（获取读锁时如果发现写锁被占用，只有写锁没有被当前线程占用的情况才会获取失败）。

**原因** : 当线程获取读锁的时候，可能有其他线程同时也在持有读锁，因此不能把获取读锁的线程“升级”为写锁；而对于获得写锁的线程，它一定独占了读写锁，因此可以继续让它获取读锁，当它同时获取了写锁和读锁后，还可以先释放写锁继续持有读锁，这样一个写锁就“降级”为了读锁。



# 9 阻塞队列 

## 9.1 BlockingQueue 简介 

`Concurrent` 包中，`BlockingQueue` 很好的解决了多线程中，如何高效安全“传输”数据的问题。通过这些高效并且线程安全的队列类，为我们快速搭建高质量的多线程程序带来极大的便利。本文详细介绍了 `BlockingQueue` 家庭中的所有成员，包括他们各自的功能以及常见使用场景。

阻塞队列，顾名思义，首先它是一个队列, 通过一个共享的队列，可以使得数据由队列的一端输入，从另外一端输出；

![10.png](./1 JUC基础篇.assets/b-10.png)

当队列是空的，从队列中获取元素的操作将会被阻塞

当队列是满的，从队列中添加元素的操作将会被阻塞

试图从空的队列中获取元素的线程将会被阻塞，直到其他线程往空的队列插入新的元素

试图向已满的队列中添加新元素的线程将会被阻塞，直到其他线程从队列中移除一个或多个元素或者完全清空，使队列变得空闲起来并后续新增

常用的队列主要有以下两种：

• 先进先出（FIFO）：先插入的队列的元素也最先出队列，类似于排队的功能。从某种程度上来说这种队列也体现了一种公平性

• 后进先出（LIFO）：后插入队列的元素最先出队列，这种队列优先处理最近发生的事件(栈)

在多线程领域：所谓阻塞，在某些情况下会挂起线程（即阻塞），一旦条件满足，被挂起的线程又会自动被唤起

为什么需要 `BlockingQueue`

好处是我们不需要关心什么时候需要阻塞线程，什么时候需要唤醒线程，因为这一切 BlockingQueue 都给你一手包办了

在 `concurrent` 包发布以前，在多线程环境下，我们每个程序员都必须去自己控制这些细节，尤其还要兼顾效率和线程安全，而这会给我们的程序带来不小的复杂度。

多线程环境中，通过队列可以很容易实现数据共享，比如经典的“生产者”和“消费者”模型中，通过队列可以很便利地实现两者之间的数据共享。假设我们有若干生产者线程，另外又有若干个消费者线程。如果生产者线程需要把准备好的数据共享给消费者线程，利用队列的方式来传递数据，就可以很方便地解决他们之间的数据共享问题。但如果生产者和消费者在某个时间段内，万一发生数据处理速度不匹配的情况呢？理想情况下，如果生产者产出数据的速度大于消费者消费的速度，并且当生产出来的数据累积到一定程度的时候，那么生产者必须暂停等待一下（阻塞生产者线程），以便等待消费者线程把累积的数据处理完毕，反之亦然。

• 当队列中没有数据的情况下，消费者端的所有线程都会被自动阻塞（挂起），直到有数据放入队列

• 当队列中填满数据的情况下，生产者端的所有线程都会被自动阻塞（挂起），直到队列中有空的位置，线程被自动唤醒



## 9.2 BlockingQueue 核心方法 

![11.png](./1 JUC基础篇.assets/b-11.png)

**BlockingQueue 的核心方法**：

**1.放入数据**

- offer(anObject):表示如果可能的话,将 anObject 加到 BlockingQueue 里,即如果 BlockingQueue 可以容纳,则返回 true,否则返回 false.**（本方法不阻塞当前执行方法的线程）**

- offer(E o, long timeout, TimeUnit unit)：可以设定等待的时间，如果在指定的时间内，还不能往队列中加入 BlockingQueue，则返回失败

- put(anObject):把 anObject 加到 BlockingQueue 里,如果 BlockQueue 没有空间,则调用此方法的线程被阻断直到 BlockingQueue 里面有空间再继续

**2.获取数据**

- poll(time): 取走 BlockingQueue 里排在首位的对象,若不能立即取出,**则可以等time 参数规定的时间,取不到时返回 null**

- poll(long timeout, TimeUnit unit)：从 BlockingQueue 取出一个队首的对象，如果在指定时间内，队列一旦有数据可取，则立即返回队列中的数据。否则知道时间超时还没有数据可取，返回失败。

- take(): 取走 BlockingQueue 里排在首位的对象,若 BlockingQueue 为空,**阻断进入等待状态直到 BlockingQueue 有新的数据被加入**

- drainTo(): 一次性从 BlockingQueue 获取所有可用的数据对象（还可以指定获取数据的个数），通过该方法，可以提升获取数据效率；不需要多次分批加锁或释放锁。



## 9.3 入门案例 

```java
基于上面四个方法自行书写案例测试即可
```

## 9.4 常见的 BlockingQueue 

### 9.4.1 ArrayBlockingQueue(常用) 

基于数组的阻塞队列实现，在 ArrayBlockingQueue 内部，维护了一个定长数组，以便缓存队列中的数据对象，这是一个常用的阻塞队列，除了一个定长数组外，ArrayBlockingQueue 内部还保存着两个整形变量，分别标识着队列的头部和尾部在数组中的位置。

ArrayBlockingQueue **在生产者放入数据和消费者获取数据，都是共用同一个锁对象，由此也意味着两者无法真正并行运行，这点尤其不同于LinkedBlockingQueue** ；按照实现原理来分析，ArrayBlockingQueue 完全可以采用分离锁，从而实现生产者和消费者操作的完全并行运行。

Doug Lea 之所以没这样去做，**也许是因为 ArrayBlockingQueue 的数据写入和获取操作已经足够轻巧，以至于引入独立的锁机制，除了给代码带来额外的复杂性外，其在性能上完全占不到任何便宜。**  **ArrayBlockingQueue 和 LinkedBlockingQueue 间还有一个明显的不同之处在于，前者在插入或删除元素时不会产生或销毁任何额外的对象实例，而后者则会生成一个额外的 Node 对象。** 这在长时间内需要高效并发地处理大批量数据的系统中，其对于 GC 的影响还是存在一定的区别。而在创建 ArrayBlockingQueue 时，我们还可以控制对象的内部锁是否采用公平锁，默认采用非公平锁。

==**一句话总结: 由数组结构组成的有界阻塞队列。**==



### 9.4.2 LinkedBlockingQueue(常用)

基于链表的阻塞队列，同 ArrayListBlockingQueue 类似，其内部也维持着一个数据缓冲队列（该队列由一个链表构成），当生产者往队列中放入一个数据时，队列会从生产者手中获取数据，并缓存在队列内部，而生产者立即返回；只有当队列缓冲区达到最大值缓存容量时（LinkedBlockingQueue 可以通过构造函数指定该值），才会阻塞生产者队列，直到消费者从队列中消费掉一份数据，生产者线程会被唤醒，反之对于消费者这端的处理也基于同样的原理。

而 LinkedBlockingQueue 之所以能够高效的处理并发数据，还因为 **其对于生产者端和消费者端分别采用了独立的锁来控制数据同步，这也意味着在高并发的情况下生产者和消费者可以并行地操作队列中的数据，以此来提高整个队列的并发性能。**

**ArrayBlockingQueue 和 LinkedBlockingQueue 是两个最普通也是最常用阻塞队列，一般情况下，在处理多线程间的生产者消费者问题，使用这两个类足以。**

==**一句话总结: 由链表结构组成的有界（但大小默认值为Integer.MAX_VALUE）阻塞队列。**==



### 9.4.3 DelayQueue

DelayQueue 中的元素只有当其指定的延迟时间到了，才能够从队列中获取到该元素。DelayQueue 是一个没有大小限制的队列，因此往队列中插入数据的操作（生产者）永远不会被阻塞，而只有获取数据的操作（消费者）才会被阻塞。

==**一句话总结: 使用优先级队列实现的延迟无界阻塞队列。**==



### 9.4.4 PriorityBlockingQueue

基于优先级的阻塞队列（优先级的判断通过构造函数传入的 Compator 对象来决定），但需要注意的是 PriorityBlockingQueue 并 **不会阻塞数据生产者，而只会在没有可消费的数据时，阻塞数据的消费者**。

因此使用的时候要特别注意，**生产者生产数据的速度绝对不能快于消费者消费数据的速度**，否则时间一长，会最终耗尽所有的可用堆内存空间。

在实现 PriorityBlockingQueue 时，内部控制线程同步的锁采用的是 **公平锁**。

==**一句话总结: 支持优先级排序的无界阻塞队列。**==



### 9.4.5 SynchronousQueue

一种无缓冲的等待队列，类似于无中介的直接交易，有点像原始社会中的生产者和消费者，生产者拿着产品去集市销售给产品的最终消费者，而消费者必须亲自去集市找到所要商品的直接生产者，如果一方没有找到合适的目标，那么对不起，大家都在集市等待。相对于有缓冲的 BlockingQueue 来说，少了一个中间经销商的环节（缓冲区），如果有经销商，生产者直接把产品批发给经销商，而无需在意经销商最终会将这些产品卖给那些消费者，由于经销商可以库存一部分商品，因此相对于直接交易模式，总体来说采用中间经销商的模式会吞吐量高一些（可以批量买卖）；但另一方面，又因为经销商的引入，使得产品从生产者到消费者中间增加了额外的交易环节，单个产品的及时响应性能可能会降低。

声明一个 SynchronousQueue 有两种不同的方式，它们之间有着不太一样的行为。

**公平模式和非公平模式的区别:**

- 公平模式：SynchronousQueue 会采用公平锁，并配合一个 FIFO 队列来阻塞多余的生产者和消费者，从而体系整体的公平策略；

- 非公平模式（SynchronousQueue 默认）：SynchronousQueue 采用非公平锁，同时配合一个 LIFO 队列来管理多余的生产者和消费者，而后一种模式，如果生产者和消费者的处理速度有差距，则很容易出现饥渴的情况，即可能有某些生产者或者是消费者的数据永远都得不到处理。

==**一句话总结: 不存储元素的阻塞队列，也即单个元素的队列。**==



### 9.4.6 LinkedTransferQueue

LinkedTransferQueue 是一个由链表结构组成的无界阻塞 TransferQueue 队列。相对于其他阻塞队列，LinkedTransferQueue 多了 tryTransfer 和transfer 方法。

LinkedTransferQueue 采用一种预占模式。意思就是消费者线程取元素时，如果队列不为空，则直接取走数据，若队列为空，那就生成一个节点（节点元素为 null）入队，然后消费者线程被等待在这个节点上，后面生产者线程入队时发现有一个元素为 null 的节点，生产者线程就不入队了，直接就将元素填充到

该节点，并唤醒该节点等待的线程，被唤醒的消费者线程取走元素，从调用的

方法返回。

==**一句话总结: 由链表组成的无界阻塞队列。**==



### 9.4.7 LinkedBlockingDeque

LinkedBlockingDeque 是一个由链表结构组成的双向阻塞队列，即可以从队列的两端插入和移除元素。

对于一些指定的操作，在插入或者获取队列元素时如果队列状态不允许该操作可能会阻塞住该线程直到队列状态变更为允许操作，这里的阻塞一般有两种情况

- 插入元素时: 如果当前队列已满将会进入阻塞状态，一直等到队列有空的位置时再讲该元素插入，该操作可以通过设置超时参数，超时后返回 false 表示操作失败，也可以不设置超时参数一直阻塞，中断后抛出 InterruptedException 异常

- 读取元素时: 如果当前队列为空会阻塞住直到队列不为空然后返回元素，同样可以通过设置超时参数

==**一句话总结: 由链表组成的双向阻塞队列**==



## 9.5 小结

**1. 在多线程领域：所谓阻塞，在某些情况下会挂起线程（即阻塞），一旦条件满足，被挂起的线程又会自动被唤起**

**2. 为什么需要 BlockingQueue?** 在 concurrent 包发布以前，在多线程环境下，我们每个程序员都必须去自己控制这些细节，尤其还要兼顾效率和线程安全，而这会给我们的程序带来不小的复杂度。使用后我们不需要关心什么时候需要阻塞线程，什么时候需要唤醒线程，因为这一切 BlockingQueue 都给你一手包办了



# 10 ThreadPool 线程池 

## 10.1 线程池简介

线程池（英语：thread pool）：一种线程使用模式。线程过多会带来调度开销，进而影响缓存局部性和整体性能。而线程池维护着多个线程，等待着监督管理者分配可并发执行的任务。这避免了在处理短时间任务时创建与销毁线程的代价。线程池不仅能够保证内核的充分利用，还能防止过分调度。

例子： 10 年前单核 CPU 电脑，假的多线程，像马戏团小丑玩多个球，CPU 需要来回切换。 现在是多核电脑，多个线程各自跑在独立的 CPU 上，不用切换效率高。

**线程池的优势：** 线程池做的工作只要是控制运行的线程数量，处理过程中将任务放入队列，然后在线程创建后启动这些任务，如果线程数量超过了最大数量，超出数量的线程排队等候，等其他线程执行完毕，再从队列中取出任务来执行。

**它的主要特点为：**

- 降低资源消耗: 通过重复利用已创建的线程降低线程创建和销毁造成的销耗。

- 提高响应速度: 当任务到达时，任务可以不需要等待线程创建就能立即执行。

- 提高线程的可管理性: 线程是稀缺资源，如果无限制的创建，不仅会销耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

- **Java** **中的线程池是通过** **Executor** **框架实现的，该框架中用到了** **Executor，Executors，ExecutorService，ThreadPoolExecutor** **这几个类**

<img src="./1 JUC基础篇.assets/b-12.png" alt="12.png" style="zoom:67%;" />



## 10.2 线程池参数说明

本次介绍 5 种类型的线程池

**<font color='bb000'>源码本质都是调用 `ThreadPollExecutor` 的构造方法去创建，阿里巴巴开发规范也建议使用这个方法创建而不是 `Executors`</font>**

```java
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) { 
-----------------------------------------------------------------------------        
    }
```

### 10.2.1 <font color='bb000'>常用参数(重点)</font>

- **`int corePoolSize` 线程池的核心线程数**

- **`int maximumPoolSize` 能容纳的最大线程数**

- **`long keepAliveTime` 空闲线程存活时间**

- **`TimeUnit unit` 存活的时间单位**

- **`BlockingQueue <Runnable> workQueue` 存放提交但未执行任务的队列**

- **`ThreadFactory threadFactory` 创建线程的工厂类**

- **`RejectedExecutionHandler handler` 等待队列满后的拒绝策略**

线程池中，有三个重要的参数，决定影响了拒绝策略：corePoolSize - 核心线程数，也即最小的线程数。workQueue - 阻塞队列 。

**maximumPoolSize -最大线程数**

当提交任务数大于 corePoolSize 的时候，会优先将任务放到 workQueue 阻塞队列中。当阻塞队列饱和后，会扩充线程池中线程数，直到达到maximumPoolSize 最大线程数配置。此时，再多余的任务，则会触发线程池

的拒绝策略了。

总结起来，也就是一句话

**当提交的任务数大于（ `workQueue.size() + maximumPoolSize` ），就会触发线程池的拒绝策略**。



### 10.2.2 拒绝策略(重点)

**CallerRunsPolicy**: 当触发拒绝策略，只要线程池没有关闭的话，则使用调用线程直接运行任务。一般并发比较小，性能要求不高，不允许失败。但是，由于调用者自己运行任务，如果任务提交速度过快，可能导致程序阻塞，性能效率上必然的损失较大

**AbortPolicy**: 丢弃任务，并抛出拒绝执行 `RejectedExecutionException` 异常信息。线程池默认的拒绝策略。必须处理好抛出的异常，否则会打断当前的执行流程，影响后续的任务执行。

**DiscardPolicy**: 直接丢弃，其他啥都没有

**DiscardOldestPolicy**: 当触发拒绝策略，只要线程池没有关闭的话，丢弃阻塞队列 workQueue 中最老的一个任务，并将新任务加入



## 10.3 线程池的种类与创建

讲下简单案例：`Single` 效率太低 `pass`，用 `cache` 大量线程同时生成，池子处理速度赶不上线程生成速度导致池子越来越大 `pass`，最后用的`fixed`（结合了cpu核心+单次处理数据限制）



### 10.3.1 newCachedThreadPool(常用)【可扩容 根据需求创建】

**作用**：创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程.

**特点**: 

- 线程池中数量没有固定，可达到最大值（Interger. MAX_VALUE）

- 线程池中的线程可进行缓存重复利用和回收（回收默认时间为 1 分钟）

- 当线程池中，没有可用线程，会重新创建一个线程

**创建方式：**

```java
public class ThreadPoolDemo1 {
    public static void main(String[] args) {

        // 创建 可自动扩容线程池
        ExecutorService cachedThreadPool = Executors.newCachedThreadPool();

        // 模拟十个请求完成任务
        try {
            for (int i = 1; i <= 10; i++) {
                cachedThreadPool.execute(() -> {
                    System.out.println(Thread.currentThread().getName() + " :: is working...");
                    try {
                        TimeUnit.SECONDS.sleep(1);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                    System.out.println(Thread.currentThread().getName() + " :: have finished working...");
                });
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            cachedThreadPool.shutdown();
        }
    }
}
```

**创建了10个线程完成这十个任务**

```apl
pool-2-thread-1 :: is working...
pool-2-thread-4 :: is working...
pool-2-thread-6 :: is working...
pool-2-thread-5 :: is working...
pool-2-thread-3 :: is working...
pool-2-thread-7 :: is working...
pool-2-thread-2 :: is working...
pool-2-thread-8 :: is working...
pool-2-thread-9 :: is working...
pool-2-thread-10 :: is working...
pool-2-thread-9 :: have finished working...
pool-2-thread-3 :: have finished working...
pool-2-thread-5 :: have finished working...
pool-2-thread-2 :: have finished working...
pool-2-thread-6 :: have finished working...
pool-2-thread-7 :: have finished working...
pool-2-thread-8 :: have finished working...
pool-2-thread-1 :: have finished working...
pool-2-thread-10 :: have finished working...
pool-2-thread-4 :: have finished working...
```

**场景:** 适用于创建一个可无限扩大的线程池，服务器负载压力较轻，执行时间较短，任务多的场景



### 10.3.2 newFixedThreadPool(常用)【一池N线程】

**作用**：创建一个可重用固定线程数的线程池，以共享的无界队列方式来运行这些线程。在任意点，在大多数线程会处于处理任务的活动状态。如果在所有线程处于活动状态时提交附加任务，则在有可用线程之前，附加任务将在队列中等待。如果在关闭前的执行期间由于失败而导致任何线程终止，那么一个新线程将代替它执行后续的任务（如果需要）。在某个线程被显式地关闭之前，池中的线程将一直存在。

**特征：**

- 线程池中的线程处于一定的量，可以很好的控制线程的并发量

- 线程可以重复被使用，在显示关闭之前，都将一直存在

- 超出一定量的线程被提交时候需在队列中等待

**创建方式**：

```java
public class ThreadPoolDemo1 {
    public static void main(String[] args) {

        // 创建 一池五线程的线程池
        ExecutorService fixedThreadPool = Executors.newFixedThreadPool(5);

        // 模拟十个请求完成任务
        try {
            for (int i = 1; i <= 10; i++) {
                fixedThreadPool.execute(() -> {
                    System.out.println(Thread.currentThread().getName() + " :: is working...");
                    try {
                        TimeUnit.SECONDS.sleep(1);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                    System.out.println(Thread.currentThread().getName() + " :: have finished working...");
                });
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            fixedThreadPool.shutdown();
        }
    }
}
```

```apl
pool-1-thread-2 :: is working...
pool-1-thread-5 :: is working...
pool-1-thread-1 :: is working...
pool-1-thread-3 :: is working...
pool-1-thread-4 :: is working...
pool-1-thread-4 :: have finished working...
pool-1-thread-1 :: have finished working...
pool-1-thread-3 :: have finished working...
pool-1-thread-2 :: have finished working...
pool-1-thread-5 :: have finished working...
pool-1-thread-5 :: is working...
pool-1-thread-2 :: is working...
pool-1-thread-4 :: is working...
pool-1-thread-1 :: is working...
pool-1-thread-3 :: is working...
pool-1-thread-3 :: have finished working...
pool-1-thread-1 :: have finished working...
pool-1-thread-4 :: have finished working...
pool-1-thread-2 :: have finished working...
pool-1-thread-5 :: have finished working...
```



**场景:** 适用于可以预测线程数量的业务中，或者服务器负载较重，对线程数有严格限制的场景



### 10.3.3 newSingleThreadExecutor(常用)【一池单线程】

**作用**：创建一个使用单个 worker 线程的 Executor，以无界队列方式来运行该线程。（注意，如果因为在关闭前的执行期间出现失败而终止了此单个线程，那么如果需要，一个新线程将代替它执行后续的任务）。可保证顺序地执行各个任务，并且在任意给定的时间不会有多个线程是活动的。**与其他等效的newFixedThreadPool 不同，可保证无需重新配置此方法所返回的执行程序即可使用其他的线程。**

**特征：** 线程池中最多执行 1 个线程，之后提交的线程活动将会排在队列中以此执行

**创建方式：**

```java
public class ThreadPoolDemo1 {
    public static void main(String[] args) {

        // 创建 单池单线程线程池
        ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();

        // 模拟十个请求完成任务
        try {
            for (int i = 1; i <= 10; i++) {
                singleThreadExecutor.execute(() -> {
                    System.out.println(Thread.currentThread().getName() + " :: is working...");
                    try {
                        TimeUnit.SECONDS.sleep(1);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                    System.out.println(Thread.currentThread().getName() + " :: have finished working...");
                });
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            singleThreadExecutor.shutdown();
        }
    }
}
```

**场景:** 适用于需要保证顺序执行各个任务，并且在任意时间点，不会同时有多个线程的场景



### 10.3.4 newScheduleThreadPool(了解)

**作用:** 线程池支持定时以及周期性执行任务，创建一个 `corePoolSize` 为传入参数，最大线程数为整形的最大数的线程池

**特征:**

（1）线程池中具有指定数量的线程，即便是空线程也将保留 

（2）可定时或者延迟执行线程活动

**场景:** 适用于需要多个后台线程执行周期任务的场景



### 10.3.5 newWorkStealingPool

jdk1.8 提供的线程池，底层使用的是 `ForkJoinPool` 实现，创建一个拥有多个任务队列的线程池，可以减少连接数，创建当前可用 `cpu` 核数的线程来并行执行任务

**场景:** 适用于大耗时，可并行执行的场景



## 10.4 线程池入门案例

**场景: 火车站 3 个售票口, 10 个用户买票**

## 10.5 线程池底层工作原理(重要)

<img src="./1 JUC基础篇.assets/b-13.png" alt="13.png" style="zoom:67%;" />

1. **在创建了线程池后，线程池中的线程数为零**

2. **当调用 `execute()` 方法添加一个请求任务时，线程池会做出如下判断：**

   2.1 **如果正在运行的线程数量小于 `corePoolSize` ，那么马上创建线程运行这个任务；**

   2.2 **如果正在运行的线程数量大于或等于 `corePoolSize` ，那么将这个任务放入队列；** 

   2.3 **如果这个时候队列满了且正在运行的线程数量还小于 `maximumPoolSize`，那么还是要创建非核心线程立刻运行这个任务；**

   2.4 **如果队列满了且正在运行的线程数量大于或等于 `maximumPoolSize`，那么线程池会启动饱和拒绝策略来执行。**

3. **当一个线程完成任务时，它会从队列中取下一个任务来执行**
4. **当一个线程无事可做超过一定的时间（`keepAliveTime`）时，线程会判断：**

4.1. **如果当前运行的线程数大于 `corePoolSize`，那么这个线程就被停掉。**

4.2. **所以线程池的所有任务完成后，它最终会收缩到 `corePoolSize` 的大小。**

![14.png](./1 JUC基础篇.assets/b-14.png)



## 10.6 注意事项(重要)

1. 项目中创建多线程时，使用常见的三种线程池创建方式，单一、可变、定长都有一定问题，原因是 `FixedThreadPool` 和 `SingleThreadExecutor` 底层都是用 `LinkedBlockingQueue` 实现的，这个队列最大长度为 `Integer.MAX_VALUE`，容易导致 `OOM`。所以实际生产一般自己通过 `ThreadPoolExecutor` 的 7 个参数，自定义线程池

2. 创建线程池推荐适用 `ThreadPoolExecutor` 及其 7 个参数手动创建

- **`int corePoolSize` 线程池的核心线程数**

- **`int maximumPoolSize` 能容纳的最大线程数**

- **`long keepAliveTime` 空闲线程存活时间**

- **`TimeUnit unit` 存活的时间单位**

- **`BlockingQueue <Runnable> workQueue` 存放提交但未执行任务的队列**

- **`ThreadFactory threadFactory` 创建线程的工厂类**

- **`RejectedExecutionHandler handler` 等待队列满后的拒绝策略**

3. 为什么不允许 Executors.的方式手动创建线程池,如下图

![15.png](./1 JUC基础篇.assets/b-15.png)



## 10.7 自定义线程池

```java
    public static void main(String[] args) {
        ThreadPoolExecutor myThreadPool = new ThreadPoolExecutor(
                2,
                5,
                2L,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()
                );
    }
```



# 11 Fork/Join 

## 11.1 Fork/Join 框架简介

Fork/Join 它可以将一个大的任务拆分成多个子任务进行并行处理，最后将子任务结果合并成最后的计算结果，并进行输出。Fork/Join 框架要完成两件事情：

**Fork：把一个复杂任务进行分拆，大事化小**

**Join：把分拆任务的结果进行合并**

<img src="./1 JUC基础篇.assets/b-16.png" alt="16.png" style="zoom:67%;" />

1. **任务分割**：首先 Fork/Join 框架需要把大的任务分割成足够小的子任务，如果子任务比较大的话还要对子任务进行继续分割

2. **执行任务并合并结果**：分割的子任务分别放到双端队列里，然后几个启动线程分别从双端队列里获取任务执行。子任务执行完的结果都放在另外一个队列里，启动一个线程从队列里取数据，然后合并这些数据。在 `Java` 的 `Fork/Join` 框架中，使用两个类完成上述操作

**ForkJoinTask**:我们要使用 `Fork/Join` 框架，首先需要创建一个 `ForkJoin` 任务。该类提供了在任务中执行 `fork` 和 `join` 的机制。通常情况下我们不需要直接集成 `ForkJoinTask` 类，只需要继承它的子类，`Fork/Join` 框架提供了两个子类：

 `a.RecursiveAction`：用于没有返回结果的任务

 `b.RecursiveTask`:用于有返回结果的任务

- **ForkJoinPool**: `ForkJoinTask` 需要通过 `ForkJoinPool` 来执行

- **RecursiveTask**: 继承后可以实现递归(自己调自己)调用的任务

**Fork/Join 框架的实现原理**

`ForkJoinPool` 由 `ForkJoinTask` 数组和 `ForkJoinWorkerThread` 数组组成，`ForkJoinTask` 数组负责将存放以及将程序提交给 `ForkJoinPool`，而`ForkJoinWorkerThread` 负责执行这些任务。



## 11.2 Fork 方法

<img src="./1 JUC基础篇.assets/b-17.png" alt="17.png" style="zoom:67%;" />

<img src="./1 JUC基础篇.assets/b-18.png" alt="18.png" style="zoom:50%;" />

**Fork 方法的实现原理：** 当我们调用 `ForkJoinTask` 的 `fork` 方法时，程序会把任务放在 `ForkJoinWorkerThread` 的 `pushTask` 的 **workQueue** 中，异步地执行这个任务，然后立即返回结果

```java
    public final ForkJoinTask<V> fork() {
        Thread t;
        if ((t = Thread.currentThread()) instanceof ForkJoinWorkerThread)
            ((ForkJoinWorkerThread)t).workQueue.push(this);
        else
            ForkJoinPool.common.externalPush(this);
        return this;
    }
```

`pushTask` 方法把当前任务存放在 `ForkJoinTask` 数组队列里。然后再调用 `ForkJoinPool` 的 `signalWork()` 方法唤醒或创建一个工作线程来执行任务。代码如下：

```java
final void push(ForkJoinTask<?> task) {
    ForkJoinTask<?>[] a; ForkJoinPool p;
    int b = base, s = top, n;
    if ((a = array) != null) { // ignore if queue removed
        int m = a.length - 1; // fenced write for task visibility
        U.putOrderedObject(a, ((m & s) << ASHIFT) + ABASE, task);
        U.putOrderedInt(this, QTOP, s + 1);
        if ((n = s - b) <= 1) {
        if ((p = pool) != null)
        	p.signalWork(p.workQueues, this);//执行
    	}
    else if (n >= m)  growArray();
    }
}
```



## 11.3 join 方法

Join 方法的主要作用是阻塞当前线程并等待获取结果。让我们一起看看ForkJoinTask 的 join 方法的实现，代码如下：

```java
public final V join() {
     int s;
     if ((s = doJoin() & DONE_MASK) != NORMAL)
     	reportException(s);
     return getRawResult();
 }
```

它首先调用 `doJoin` 方法，通过 `doJoin()` 方法得到当前任务的状态来判断返回什么结果，任务状态有 4 种：

**==已完成（NORMAL）、被取消（CANCELLED）、信号（SIGNAL）和出现异常（EXCEPTIONAL）==**

- 如果任务状态是已完成，则直接返回任务结果。

- 如果任务状态是被取消，则直接抛出 `CancellationException`

- 如果任务状态是抛出异常，则直接抛出对应的异常

让我们分析一下 `doJoin` 方法的实现

```java
    private int doJoin() {
        int s;
        Thread t;
        ForkJoinWorkerThread wt;
        ForkJoinPool.WorkQueue w;
        return (s = status) < 0 ? s :
                ((t = Thread.currentThread()) instanceof ForkJoinWorkerThread) ?
                        (w = (wt = (ForkJoinWorkerThread) t).workQueue).
                                tryUnpush(this) && (s = doExec()) < 0 ? s :
                                wt.pool.awaitJoin(w, this, 0L) :
                        externalAwaitDone();
    }

    final int doExec() {
        int s;
        boolean completed;
        if ((s = status) >= 0) {
            try {
                completed = exec();
            } catch (Throwable rex) {
                return setExceptionalCompletion(rex);
            }
            if (completed)
                s = setCompletion(NORMAL);
        }
        return s;
    }
```

在 `doJoin()` 方法流程如下:

1. 首先通过查看任务的状态，看任务是否已经执行完成，如果执行完成，则直接返回任务状态；

2. 如果没有执行完，则从任务数组里取出任务并执行。
3. 如果任务顺利执行完成，则设置任务状态为 `NORMAL`，如果出现异常，则记录异常，并将任务状态设置为 `EXCEPTIONAL`。



## 11.4 Fork/Join 框架的异常处理

`ForkJoinTask` 在执行的时候可能会抛出异常，但是我们没办法在主线程里直接捕获异常，所以 `ForkJoinTask` 提供了 `isCompletedAbnormally()` 方法来检查任务是否已经抛出异常或已经被取消了，并且可以通过 `ForkJoinTask` 的 `getException` 方法获取异常。

`getException` 方法返回 `Throwable` 对象，如果任务被取消了则返回 `CancellationException`。如果任务没有完成或者没有抛出异常则返回 `null`。



## 11.5 入门案例

**场景: 生成一个计算任务，计算 1+2+3.........+1000**, **==每 100 个数切分一个子任务==**

```java
class myTask extends RecursiveTask<Integer> {

    private int VALUE = 10;
    private int begin;
    private int end;
    private int result;

    public myTask(int begin, int end) {
        this.begin = begin;
        this.end = end;
    }

    @Override
    protected Integer compute() {
        System.out.println("任务 : " + begin + " ========= " + end + " 累加开始");

        if (end - begin + 1 <= VALUE) {
            for (int i = begin; i <= end; i++) {
                result += i;
            }
        } else {
            int mid = begin + end >> 1;

            // 划分为两个子任务
            myTask myTask1 = new myTask(begin, mid);
            myTask myTask2 = new myTask(mid + 1, end);

            // 异步执行
            myTask1.fork();
            myTask2.fork();

            // 同步阻塞获取执行结果
            result = myTask1.compute() + myTask2.compute();
        }

        return result;
    }
}

public class ForkJoinDemo {

    public static void main(String[] args) throws ExecutionException, InterruptedException {

        ForkJoinPool forkJoinPool = null;

        try {
            // 定义任务
            myTask myTask = new myTask(1, 100);

            // 定义执行对象
            forkJoinPool = new ForkJoinPool();

            // 加入任务执行
            ForkJoinTask<Integer> result = forkJoinPool.submit(myTask);

            // 输出结果
            System.out.println(result.get());
        } finally {
            forkJoinPool.shutdown();
        }
    }
}
```



# 12 CompletableFuture 

## 12.1 CompletableFuture 简介

`CompletableFuture` 在 `Java` 里面被用于异步编程，异步通常意味着非阻塞，可以使得我们的任务单独运行在与主线程分离的其他线程中，并且通过回调可以在主线程中得到异步任务的执行状态，是否完成，和是否异常等信息。

`CompletableFuture` 实现了 `Future`, `CompletionStage` 接口，实现了 Future接口就可以兼容现在有线程池框架，而 `CompletionStage` 接口才是异步编程的接口抽象，里面定义多种异步方法，通过这两者集合，从而打造出了强大的 `CompletableFuture` 类。



## 12.2 Future 与 CompletableFuture

`Futrue` 在 `Java` 里面，通常用来表示一个异步任务的引用，比如我们将任务提交到线程池里面，然后我们会得到一个 `Futrue`，在 `Future` 里面有 `isDone` 方法来 判断任务是否处理结束，还有 `get` 方法可以一直阻塞直到任务结束然后获取结果，但整体来说这种方式，还是同步的，因为需要客户端不断阻塞等待或者不断轮询才能知道任务是否完成。

**Future 的主要缺点如下：**

（1）**不支持手动完成**

我提交了一个任务，但是执行太慢了，我通过其他路径已经获取到了任务结果，现在没法把这个任务结果通知到正在执行的线程，所以必须主动取消或者一直等待它执行完成

（2）**不支持进一步的非阻塞调用**

通过 `Future` 的 `get` 方法 **会一直阻塞到任务完成，但是想在获取任务之后执行额外的任务，因为 `Future` 不支持回调函数，所以无法实现这个功能**

（3）**不支持链式调用**

对于 `Future` 的执行结果，我们想继续传到下一个 `Future` 处理使用，从而形成一个链式的 `pipline` 调用，这在 `Future` 中是没法实现的。

（4）**不支持多个 `Future` 合并**

比如我们有 `10` 个 `Future` 并行执行，我们想在所有的 `Future` 运行完毕之后，执行某些函数，是没法通过 `Future` 实现的。

（5）**不支持异常处理**

`Future` 的 `API` 没有任何的异常处理的 `api`，所以在异步运行时，如果出了问题是不好定位的。



## 12.3 CompletableFuture 入门

### 12.3.1 使用 CompletableFuture

**场景:主线程里面创建一个 CompletableFuture，然后主线程调用 get 方法会阻塞，最后我们在一个子线程中使其终止。**



### 12.3.2 没有返回值的异步任务

```java
public class CompletableFutureDemo {

    public static void main(String[] args) throws Exception {
        CompletableFuture<Void> completableFuture1 = CompletableFuture.runAsync(() -> {
            System.out.println(Thread.currentThread().getName() + " :: completableFuture1");
        });

        completableFuture1.get();
    }
}
```



### 12.3.3 有返回值的异步任务

**这里异步返回的两个参数 一个是返回值，一个是运行过程中的异常信息。**

```java
public class CompletableFutureDemo {

    public static void main(String[] args) throws Exception {

        CompletableFuture<Integer> completableFuture2 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + " :: completableFuture2");
            return 1024;
        });

        completableFuture2.whenComplete((t, e) -> {
            System.out.println("t = " + t);
            System.out.println("e = " + e);
        });
    }
}
```













