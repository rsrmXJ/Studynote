# Java 高级

### 第一章 多线程

##### 1. 基本概念：程序、进程、线程

**程序 (program)**：程序是为完成特定任务，用某种计算机语言所编写的一组有顺序的指令与数据的集合。程序是一段静态的代码。

**进程（process）** 是程序的一次执行过程、或正在运行中的程序。动态的过程：程序加载内存，执行、销毁的过程。——生命周期 

- 程序静态的，而进程是动态。
- 进程作为资源分配的单位，系统再运行时会为每个进程分配不同的内存区域

**线程（thread）**

进程可以进一步细化为线程，**是一个程序内部的一条执行路径** 。

- 若一个进程同一时间并行执行多个线程，就是支持多线程的。
- 线程作为调度和执行的单位，**每个线程拥有独立的运行栈和程序计数器**，线程切换的开销小。
- 一个进程中的多个线程共享相同的内存单元/单元地址空间，它们在同一堆中分配对象。可以访问相同的变量对象。这就使得线程间通信更加简便、高效。但多个线程共享的系统资源可能带来安全隐患。

**单核 CPU 和多核 CPU 的理解**

- 单核 CPU 是一种假的多线程，因为在一个时间单元内，也只能执行一个线程的任务。
- 多核CPU，才能更好的发挥多线程的效率。
- 一个 Java 应用程序，至少有三个线程： main()  主线程， gc() 垃圾回收线程，异常处理线程。当然如果发生异常，会影响主线程。

**并行与并发**

- 并行：多个 CPU 同时执行多个任务。（多个人同时做不同的事。）
- 并发：一个 CPU (采用时间段) “同时” 执行多个的任务。（场景：多个人同时做一件事：抢票、秒杀。）

**使用多线程的优点**：

以单核 CPU 为例，使用单个线程完成多个任务，肯定比用多个线程来完成的时间更短,为何需要多线程？

>  我们需要完成多个任务，若采用单线程，CPU 可以执行完这个任务之后执行另一个任务。若是采用多线程，还要再不同的任务之间来回切换。进而影响效率。

优点：

1. 提高应用程序的响应速度。对图形化界面更有意义，可增强用户体验。
2. 提高计算机系统 CPU 的利用率。
3. 改善程序结构。将既长且复杂的进程分为多个线程，多个线程，独立运行，利于理解和修改。

**何时需要多线程？**

1. 程序需要同时执行两个或多个任务。例如：我们在电脑管家中既要清除垃圾，又要扫描病毒。

2. 程序需要实现一些需要等待的任务时。如：用户输入、文件读写操作、网络操作，搜索等。

   > 例如：我们需要在一个软件中打开某个界面，而某些资源需要从网络中获取下载，同时用户还要进行其它操作。

3. 需要一些后台操作的程序时。

##### 2. 多线程的创建与使用 

- Java 语言的 JVM 允许程序运行多个线程，它通过 Java.lang.Thread 类来实现。

- Thread 类的特性：
  - 每个线程都是通过某个特定的 Thread 对象的 run()方法来完成操作。通常把 run() 方法的主体称为线程体
  - -通过该 Thread 对象的 start() 方法来启动这个线程

**Thread 类的构造器：**

- Thread() 创建新的 Thread 对象
- Thread(String threadName) 创建线程并指定线程实例名
- Thread(Runnable target) 创建指定线程的目标对象，它实现了 Runnable 接口。（也就是这里的 target 实现了 Runnable 接口）
- Thread(Runnable target,String name) 创建新的 Thread 对象

JDK 1.5 之前创建线程的两种方式：

- 继承 Thread 类的方式
- 实现 Runnable 接口的方式

**方式一：继承 Thread 类**

1. 创建一个类继承于 Thread 类
2. 重写 Thread 类的 run() //线程的操作写在 run() 的方法体中
3. 创建 Thread 子类的对象。
4. 通过此对象调用 start() //1.启动当前线程 2. 调用线程的 run() 方法

注意：

1. 我们不能通过 `对象.run()` 来启动线程，这样就是只是普通方法， 并没有创建多线程。
2. 在多线程中 run() 方法由 JVM 调用，什么时候调用，执行的操作过程都由操作系统的 CPU 调度决定
3. 想要启动多线程，必须调用 `start()` 方法
4. 一个线程对象只能调用一次 `start()` 方法启动，如果向再次调用，必须重建一个 Thread 类的对象,然后通过 `对象.start()` 再次执行。否则将会报出异常 `IllegalthreadStarteException`

```java
public class ThreadDemo {
    public static void main(String[] args) {
          MyThread1 m1 = new MyThread1();
          MyThread2 m2 = new MyThread2();
        
          m1.start();
          m2.start();
    }
}

class MyThread1 extends Thread{
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            if(i % 2 == 0){
                System.out.println(Thread.currentThread().getName() + ":" + i);

            }
        }

    }
}


class MyThread2 extends Thread{
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            if(i % 2 != 0){
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
     }
    
}
    
}
```

第二种：创建 Thread 的匿名子类

```java
public class ThreadTest extends Thread {
    public static void main(String[] args) {
        
        @Override
            public void run() {

                for (int i = 0; i < 100; i++) {
                    if(i % 2 == 0){
             \           System.out.println(Thread.currentThread().getName() + ":" + i);
                    }
                }
            }
        }.start();

        new Thread(){
            @Override
            public void run() {
                for (int i = 0; i < 100; i++) {
                    if(i % 2 != 0){
                        System.out.println(Thread.currentThread().getName() + ":" + i);

                    }
                }
            }
      }.start();
    
	}

}
```

**方式二： 实现 Runnable 接口**：

1. 创建一个实现 Runnable 接口的类
2. 实现类去实现 Runnable 中的抽象方法 run()
3. 创建实现类的对象。
4. 将此对象做参数传递到 Thread 类的构造器中，创建 Thread 类的对象。
5. 通过此对象调用 start();

两种创建方式的对比：

开发中优先选择实现 Runnable 接口的方式，

原因：

1. 实现的方式没有类的单继承的局限性（另一种方式只能继承于 Thread 类，而不能继承其它类）。
2. 更适合处理多个线程又共享数据的情况。

```java
例子
calss MThread implements Runnable{
    
    @override
    public void run(){    
        for (int i = 0; i < 100; i++) {
            if(i % 2 == 0){
       			System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        }
    }
    
} 
public class ThreadTest{
    
    public static void main(String[] args){
        MThread mThread = new MThread();
        Thread t1 = new Thread(mThread);
        t1.start();
        //再启动一个线程
        Thread t2 = new Thread(mThread);
        t2.start();
    }
    
}
```

继承方式和实现方式的区别：

- 区别：
  - 继承 Thread：线程代码存放在 Thread 子类的 run() 犯法中
  - 实现 Runnable：线程代码存放在 Runnable 接口实现类的 run() 方法中
- 实现方式的好处（使用 Runnable 接口的好处）：
  - 好处：避免了继承的局限性
  - 多个线程可以共享一个接口，非常适合多个线程来处理同一份资源。

相同点：两种方式都需要重写 run() 方法，将线程要执行的内容声明在 run() 中。

联系：Thread 也是 Runnable 的实现类

线程的常用方法:

| 方法名称                      | 功能                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| void start()                  | 启动线程，并执行对象的 run() 方法。                          |
| run()                         | 将创建线程中要执行的操作定义在此方法中<br />*线程在被调度时执行的操作* |
| static Thread currentThread() | 返回当前线程。                                               |
| String getName()              | 得到当前线程的名字                                           |
| setName()                     | 设置当前线程的名字 //要线程执行之前进行设置                  |
| static void yeild()           | 释放当前线程 CPU 的执行权。(有可能下一次，CPU 又切换到当前线程)<br />- 暂停当前正在执行的线程，把执行机会让给优先级相同或更高的线程<br />- 若队列中没有相同优先级的线程忽略次方法 |
| join()                        | 在线程 A 中调用线程 B 的 join,此时，线程 A 进入阻塞状态(暂时不会执行)，直到线程 B 执行完毕时，线程 A 结束阻塞状态 |
| stop()                        | 强制当前线程声明周期结束，不推荐使用                         |
| static sleep(long millitimes) | 让当前线程 “休眠” 指定的millitime 毫秒.在指定的 millitime 毫秒内，当前线程处于阻塞状态 |
| boolean isAlive()             | 判断当前线程是否存活                                         |

**线程优先级的设置**

线程的调度：

- **调度策略**：
  - 时间段：cpu 按照时间段来切换线程。
  - 抢占式：高优先级的线程抢占 CPU
- Java 的调度方法：
  - 同优先级的线程组成先进先出队列,使用时间片策略。
  - 对高优先级，使用优先调度的抢占方式
- **线程的优先级等级：**
  - MAX_PRIORITY:10
  - MIN_PRIORITY:1
  - NORM_PRIORITY:5
- **设计的方法**：
  - getPriority(): 返回线程优先值
  - setPriority(int newPriority):改变线程的优先级
- **说明：**
  - 线程创建时继承父线程的优先级
  - **低优先级只是获得 CPU 获得调度概率的低，并非一定时调度高优先级线程之后才被调用**。

说明：高优先级的线程抢占低优先级线程的 CPU 的执行权，只是从概率上讲。高优先级的线程只是被执行概率很高，并不意味着高优先级的执行完毕，低优先级的线程才执行。

**线程的分类：**

Java 中的线程可以分为两类：一种时守护线程，另一种是用户线程。

当用户线程执行结束之后，守护线程也会结束。

- 守护线程是用来服务用户线程的，通过在start() 方法前调用 thread.setDaemon(true) 可以把一个用户线程变成守护线程
- 在 JVM 若都是守护线程，当前 JVM 将退出。
- Java 垃圾回收时一个典型的守护线程。

##### 3. 线程的生命周期

>- 新建：当一个 Thread 类或其子类的**对象被声明并创建时**，新的线程对象处于新建状态；新生的线程 &ne; 新建的状态。
>- 就绪：当新建(状态)的线程被 start() 后，将进入线程队列等待 CPU 时间片，此时已经具备运行条件，只是没分配到 CPU 资源。 
>- 运行：当就绪的线程被调度并获得 CPU 资源时，便进入运行状态，run() 方法定义了线程的操作和功能
>- 阻塞：在某种特殊情况下，被人为挂起或执行输入输出时，让 CPU 临时终止自己的执行进入阻塞状态
>- 死亡：线程完成了它的全部工作或线程被提前强制性地终止或出现异常导致结束。

![threadlife](C:/doc/Markdown文档/Java/threadlife.png)

说明：

>1. 声明周期关注的两个概念：状态和相应方法的执行。
>
>2. 关注：状态 A --> 状态 B：哪些方法执行了（回调方法）
>
> ​			某个方法主动调用：状态 A --> 状态 B
>
>3. 阻塞：临时状态，不可以作为最终状态
>
> 死亡：最终状态

##### 4. 线程的同步

理解线程的安全问题的举例和解决：

- 多个线程执行的不确定性导致执行结果的不确定性。(也就是说，当一个线程操作数据时，另一个线程也可能操作该数据，最终导致的执行的不正确。)
- 多个线程对数据的共享，会造成操作的不完整性，会破坏数据。

多线程安全问题出现的原因：当某个线程操作共享数据的过程中，尚未完成该操作，其它前线程又操作了该共享数据，从而出现线程的操作问题。

解决：当一个线程 A 在操作共享数据时，其它线程不能操作该数据，直到线程 A 操作完成。其它数据才可以继续操作。(即使线程 A 进入阻塞状态也不能改变)

在 Java 中通过**同步机制**，来解决线程的安全的问题。

实现 Runnable 的方式

```java
 *  方式一：同步代码块
 *
 *   synchronized(同步监视器){
 *      //需要被同步的代码
 *
 *   }
 *  说明：1.操作共享数据的代码，即为需要被同步的代码。  -->不能包含代码多了，也不能包含代码少了。仅需要包含操作共享数据的代码
 *       2.共享数据：多个线程共同操作的变量。比如：ticket 就是共享数据。
 *       3.同步监视器，俗称：锁。
     	    任何一个类的对象，都可以充当锁。
 *          要求：多个线程必须要共用同一把锁。
 *
 *       补充：
 *    	- 在实现 Runnable 接口创建多线程的方式中，我们可以考虑使用 this 作为 同步监视器。
 *	  	- 在继承 Thread 类创建多线程的方式中，慎用 this 作为同步监视器，可以考虑使用当前类（即：*.class）作为同步监视器。（如果创建多个对象 (同时不能使用 this 作为锁)，或将锁设置为静态的）
     - 注意创建锁的对象不能在 run() 方法中，否则锁的对象就不是唯一的，而是每一个线程都拥有独立的锁。
 *
 *	注：这里的锁即同步监视器；类也是对象,且类只会加载一次 (静态结构是随着的类的加载而加载，且加载一次。由  *	此可以推断，类也只会加载一次)。
 *  方式二：同步方法。
 *     如果操作共享数据的代码完整的声明在一个方法中，我们不妨将此方法声明同步的。
 *
 *  5.同步的方式，解决了线程的安全问题。---好处
 *    操作同步代码时，只能有一个线程参与，其他线程等待。相当于是一个单线程的过程，效率低。 ---局限性
 *
 * @author shkstart
 * @create 2019-02-13 下午 4:47
 */
class Window1 implements Runnable{

    private int ticket = 100;
//    Object obj = new Object();
//    Dog dog = new Dog();
    @Override
    public void run() {
//        Object obj = new Object();
        while(true){
            synchronized (this){//此时的this:唯一的Window1的对象   //方式二：synchronized (dog) {

                if (ticket > 0) {

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(Thread.currentThread().getName() + ":卖票，票号为：" + ticket);


                    ticket--;
                } else {
                    break;
                }
            }
        }
    }
}


public class WindowTest1 {
    public static void main(String[] args) {
        Window1 w = new Window1();

        Thread t1 = new Thread(w);
        Thread t2 = new Thread(w);
        Thread t3 = new Thread(w);

        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();
    }

}

class Dog{
    
}

注：这里同步选择同步代码块，因为我们调用的 Window1 的 run() 方法，使用 this 作为同步锁，因为 this 即 w 是唯一的确定的。
```

> 在继承 Thread 方式中，共享数据放在该类中。如果在该方式中使用 this 作为关键字，那么就是该类的对象做关键字。而创建多个线程需要多个不同的 Thread 类对象因此不合适。
>
> 而实现 Runnable 方式，共享数据在实现 Runnable 的类中。Thread 使用相同的(实现 Runnable 接口)类的对象做为参数，而共享数据在该类的对象中仅有一个，因此可以确保多个线程使用同一把锁。

继承 Thread 的方式：

```java
class Window2 extends Thread{

    private static int ticket = 100;

    private static Object obj = new Object();

    @Override
    public void run() {

        while(true){
            //正确的
//            synchronized (obj){
            synchronized (Window2.class){//Class clazz = Window2.class,Window2.class只会加载一次
                //错误的方式：this代表着t1,t2,t3三个对象
//              synchronized (this){

                if(ticket > 0){

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(getName() + "：卖票，票号为：" + ticket);
                    ticket--;
                }else{
                    break;
                }
            }

        }

    }
}


public class WindowTest2 {
    public static void main(String[] args) {
        Window2 t1 = new Window2();
        Window2 t2 = new Window2();
        Window2 t3 = new Window2();


        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();

    }
}
```

根据以上同步锁我们可以总结出：

- 继承 Thread 的方式：类、自定义对象必须是静态的
  - 操作的共享数据也要是静态的。否则每个对象都独自拥有一套属性

- 实现 Runnable 的方式：this、类、自定义对象

**同步范围**：

以上面的 run() 代码为例。

```java
在实现 Runnable 接口中
//错误1：包的太多了
@Override
public void run() {
    synchronized (this) {//在对其进行加锁
        while (true) {
            if (ticket > 0) {

                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                System.out.println(Thread.currentThread().getName() + ":买票，票号为" + ticket);
                ticket--;
            } else {
                break;
            }
        }
    }//到这里才对锁进行释放
}
//这个方法完全不能发挥多线程的优势，已进入到 run() 方法，就会等线程执行完毕后才会释放锁 while(true)，与我们的要求不符，就是单线程
//第二种使用同步方法的错误
 @Override
public synchronized void run() {//在这里加锁
    while (true) {
        if (ticket > 0) {

            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(Thread.currentThread().getName() + ":买票，票号为" + ticket);
            ticket--;
        } else {
            break;
        }
    }
}//这里释放
//还是只相当于一个单线程，因此我们的同步方法要声明到 run() 方法之外。

//错误3：包的还是太多了,错误同方式 2
@Override
public void run() {
    saleTicket();
}
public synchronized void saleTicket(){
    while (true) {
        if (ticket > 0) {

            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(Thread.currentThread().getName() + ":买票，票号为" + ticket);
            ticket--;
        } else {
            break;
        }
    }
}

因此包裹不能包多了
```

注意：同步方法，要声明到 run() 之外。

***方式二***：同步方法

如果**操作共享数据的代码正好完整的声明在一个方法中** ，我们可以将这个方法声明为同步的。

- 使用同步方法处理实现 Runnable 的线程安全问题。

  格式: `权限修饰符 synchronized 返回值类型方法名(){}`,//同步监视器为 this

- 使用同步方法处理继承 Thread 的线程安全问题

  我们需要将同步方法声明为 static。

```java
/**
 * 使用同步方法解决实现Runnable接口的线程安全问题
 *
 *
 *  关于同步方法的总结：
 *  1. 同步方法仍然涉及到同步监视器，只是不需要我们显式的声明。
 *  2. 非静态的同步方法，同步监视器是：this
 *     静态的同步方法，同步监视器是：当前类本身
 *
 * @author shkstart
 * @create 2019-02-15 上午 11:35
 */


class Window3 implements Runnable {

    private int ticket = 100;

    @Override
    public void run() {
        while (true) {

            show();
        }
    }

    private synchronized void show(){//同步监视器：this
        //synchronized (this){

            if (ticket > 0) {

                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                System.out.println(Thread.currentThread().getName() + ":卖票，票号为：" + ticket);

                ticket--;
            }
        //}
    }
}


public class WindowTest3 {
    public static void main(String[] args) {
        Window3 w = new Window3();

        Thread t1 = new Thread(w);
        Thread t2 = new Thread(w);
        Thread t3 = new Thread(w);

        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();
    }

}

/**
 * 使用同步方法处理继承Thread类的方式中的线程安全问题
 *
 * @author shkstart
 * @create 2019-02-15 上午 11:43
 */
class Window4 extends Thread {


    private static int ticket = 100;

    @Override
    public void run() {

        while (true) {

            show();
        }

    }
    private static synchronized void show(){//同步监视器：Window4.class
        //private synchronized void show(){ //同步监视器：t1,t2,t3。此种解决方式是错误的。因此我们需要将方法声明为静态的
        if (ticket > 0) {

            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(Thread.currentThread().getName() + "：卖票，票号为：" + ticket);
            ticket--;
        }
    }
}


public class WindowTest4 {
    public static void main(String[] args) {
        Window4 t1 = new Window4();
        Window4 t2 = new Window4();
        Window4 t3 = new Window4();


        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();

    }
}
```

总结：使用 Thread 的方式要将同步方法声明为静态的，否则将导致每个线程都拥有一套独立的属性。

同步方法

**1. synchronized  的锁是什么？**

> 第一个访问某项资源（共享数据）的任务必须锁定这项资源，使其他任务在其被解锁之前，就无法访问它了，而在其被解锁 之时，另一个任务就可以锁定并使用它了。

- 任意对象都可以作为同步锁。所有对象都自动含有单一的锁。
- 同步方法的锁： 静态方法（类名.class）,非静态方法（this）
- 同步代码块：自己制定，很多时候也是指定为 this 或类名.class

注意：

- 使用同一数据的多个线程共用一把锁，否则将无法保证共享资源的安全

**线程安全的单例模式之懒汉式**：

```java
public class BankTest {

}

class Bank{

    private Bank(){}

    private static Bank instance = null;

    public static Bank getInstance(){
        //方式一：效率稍差
//        synchronized (Bank.class) {
//            if(instance == null){
//
//                instance = new Bank();
//            }
//            return instance;
//        }
//我们只需要实例化一次就可以，后面的线程可以直接取走没有必要等待，浪费时间。上面这样做就相当于一个单线程，只有等前面的线程取走了后面的线程才能够执行。        
        //方式二：效率更高
        if(instance == null){

            synchronized (Bank.class) {
                //这里为什么要使用 Bank.class 作为同步锁
                /**
                 *疑问，以后解决
                 *因为该方法是静态的因此我们通过类名的调用该方法。如果使用 this 也是指该类名，但是我不知 Bank 和 Bank.class 有什么区别，因此这里的 this 就是 Bank.class
                 */
                if(instance == null){

                    instance = new Bank();
                }

            }
        }
        return instance;
    }
    //只要赋了一次值，后面的线程只需要一次判断就可以直接使用
}
```

**3. 同步的范围**

**代码是否存在线程安全问题**

1. 明确哪些代码是多线程运行的代码
2. 明确多个线程是否有共享数据
3. 明确多线程运行代码中，是否有多条语句操作共享数据

**如何解决**

对多条操作共享数据的语句，只能让一个线程执行，在执行过程中。其它线程不可操作共享数据。即所有操作共享数据的语句都要放在同步范围内

**范围**

- 范围太小：没锁主所有有安全问题的代码
- 范围太大：没发挥线程的优势

**4. 释放锁的操作**

- 当前线程的同步方法、同步代码块执行结束
- 当前线程在同步代码块、同步方法中遇到 break、return 终止了代码块。
- 当前线程在同步代码块、同步方法出现了未处理的 Exception 或 Error 导致异常结束
- 当前线程在同步代码块、同步方法中执行了线程对象的 wait()，当前线程暂停并释放锁。

**5.不会释放锁的操作**

- 线程执行同步代码块或方法时，程序调用 Thread.sleep(),Thread.yeild() 方法暂停当前线程的执行。

- 线程执行同步代码块时，其它线程调用了该线程的 supend() 方法将该线程挂起，该线程不会释放锁。

  尽量避免使用 suspend() 和 resume 来控制线程。

**6. 线程的死锁问题**

- 不同的线程分别占用对方需要的同步资源不放，都在等待对方放弃自己需要的同步资源，就形成了线程的死锁。

- 出现死锁后，不会出现异常，不会出现提示，只是所有的线程处于阻塞状态，无法继续。

解决方法:

- 专门的方法、原则
- 尽量减少同步资源的定义
- 尽量避免嵌套同步

注意: 运行正常不代表没有死锁。如果有嵌套同步就要注意死锁。

```java
package com.atguigu.java1;
//死锁的演示
class A {
	public synchronized void foo(B b) { //同步监视器：A类的对象：a
		System.out.println("当前线程名: " + Thread.currentThread().getName()
				+ " 进入了A实例的foo方法"); // ①
//		try {
//			Thread.sleep(200);
//		} catch (InterruptedException ex) {
//			ex.printStackTrace();
//		}
		System.out.println("当前线程名: " + Thread.currentThread().getName()
				+ " 企图调用B实例的last方法"); // ③
		b.last();//这里要使用到 B 类对象的错，如果 B 类已经取得 B 类对象的锁那么就会陷入死锁
	}

	public synchronized void last() {//同步监视器：A类的对象：a
		System.out.println("进入了A类的last方法内部");
	}
}

class B {
	public synchronized void bar(A a) {//同步监视器：b
		System.out.println("当前线程名: " + Thread.currentThread().getName()
				+ " 进入了B实例的bar方法"); // ②
//		try {
//			Thread.sleep(200);
//		} catch (InterruptedException ex) {
//			ex.printStackTrace();
//		}
		System.out.println("当前线程名: " + Thread.currentThread().getName()
				+ " 企图调用A实例的last方法"); // ④
		a.last();//这里要使用到： a类对象的锁，如果 B 类已取得 B 类对象的锁，那么也会现如死锁
	}

	public synchronized void last() {//同步监视器：b
		System.out.println("进入了B类的last方法内部");
	}
}

public class DeadLock implements Runnable {
	A a = new A();
	B b = new B();

	public void init() {
		Thread.currentThread().setName("主线程");
		// 调用a对象的foo方法
		a.foo(b);
		System.out.println("进入了主线程之后");
	}

	public void run() {
		Thread.currentThread().setName("副线程");
		// 调用b对象的bar方法
		b.bar(a);
		System.out.println("进入了副线程之后");
	}

	public static void main(String[] args) {
		DeadLock dl = new DeadLock();
		new Thread(dl).start();


		dl.init();
	}
}
总结：死锁是相对于锁而言的，如果 A 线程中用到 B 线程正在使用的锁，就会形成死锁。死锁锁的虽然共享数据，线程要取得的是对方的锁。有些代码运行正常，不代表没有死锁。
    
  public class ThreadTest {

    public static void main(String[] args) {

        StringBuffer s1 = new StringBuffer();
        StringBuffer s2 = new StringBuffer();


        new Thread(){
            @Override
            public void run() {

                synchronized (s1){

                    s1.append("a");
                    s2.append("1");

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }


                    synchronized (s2){
                        s1.append("b");
                        s2.append("2");

                        System.out.println(s1);
                        System.out.println(s2);
                    }


                }

            }
        }.start();


        new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (s2){

                    s1.append("c");
                    s2.append("3");

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    synchronized (s1){
                        s1.append("d");
                        s2.append("4");

                        System.out.println(s1);
                        System.out.println(s2);
                    }


                }



            }
        }).start();


    }


}

```



**线程的同步**：

- JDK 5.0 开始，Java 提供了更加强大的线程同步机制——通过显式定义同步所对象来实现同步。同步所使用 Lock 对象充当。
- java.util.concurrent.locks.lock 接口是控制多个线程对共享资源的访问工具。锁提供了对共享资源的独占访问，每次只能有一个对象对 Lock 对象加锁，线程开始访问共享资源之前应先获得 Lock 对象
- ReentrantLock 类实现 Lock,它拥有与 synchronized 相同的并发性和内存语义，在实现线程安全的控制中，比较常用的 ReentrantLock,可以显式加锁、释放锁。

*解决线程安全的问题方式三*：

1. ```java
   public class A {
   
       private final ReentrantLock lock = new ReentrantLock();
       public void m(){
           try{
               lock.lock();
               //保证线程安全的代码
           }finally {
               lock.unlock();
           }
       }
   
   }
   //注意：如果同步代码有异常，要将unlock()写入finally语句块
   ```

synchronized 和 lock() 的异同：

相同：两者都可以解决线程的安全问题

不同：

- synchronized 是隐式锁，在执行完相应的同步代码以后，自动释放同步监视器。

- lock 是显式锁，需要手动的启动同步（lock()），同时结束同步也需要手动的实现（unlock()）
- lock 只有代码块锁，而 synchronized 有方法锁和代码块锁
- 使用 Lock 锁， JVM 将花费较少的时间来调度线程，性能更好。并且具有更好的扩展性(提供更多的子类)

优先使用顺序： lock >> 同步代码块 >> 同步方法

##### 5. 线程的通信

```java
/**
 * 线程通信的例子：使用两个线程打印 1-100。线程1, 线程2 交替打印
 *
 * 涉及到的三个方法：
 * wait():一旦执行此方法，当前线程就进入阻塞状态，并释放同步监视器。
 * notify():一旦执行此方法，就会唤醒被 wait() 的一个线程。如果有多个线程被wait，就唤醒优先级高的那个。
 * notifyAll():一旦执行此方法，就会唤醒所有被 wait() 的线程。
 *
 * 说明：
 * 1.wait()，notify()，notifyAll()三个方法必须使用在 synchronized 同步代码块或 synchonized 同步方法中。
 * 2.wait()，notify()，notifyAll()三个方法的调用者必须是锁对象。
 *    否则，会出现IllegalMonitorStateException异常
 * 3.wait()，notify()，notifyAll()三个方法是定义在java.lang.Object类中。
 *
 * 面试题：sleep() 和 wait()的异同？
 * 1.相同点：一旦执行方法，都可以使得当前的线程进入阻塞状态。
 * 2.不同点：1）两个方法声明的位置不同：Thread类中声明sleep() , Object类中声明wait()
 *          2）调用的要求不同：sleep()可以在任何需要的场景下调用。 wait()必须使用在同步代码块或同步方法中
 *          3）关于是否释放同步监视器：如果两个方法都使用在同步代码块或同步方法中，sleep()不会释放锁，wait()会释放锁。
 *
 * @author shkstart
 * @create 2019-02-15 下午 4:21
 */
class Number implements Runnable{
    private int number = 1;
    private Object obj = new Object();
    @Override
    public void run() {

        while(true){

            synchronized (obj) {

                obj.notify();

                if(number <= 100){

                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(Thread.currentThread().getName() + ":" + number);
                    number++;

                    try {
                        //使得调用如下wait()方法的线程进入阻塞状态
                        obj.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                }else{
                    break;
                }
            }

        }

    }
}


public class CommunicationTest {
    public static void main(String[] args) {
        Number number = new Number();
        Thread t1 = new Thread(number);
        Thread t2 = new Thread(number);

        t1.setName("线程1");
        t2.setName("线程2");

        t1.start();
        t2.start();
    }
}

```

**生产者\消费者问题**

##### 6. JDK 5.0 新增的线程创建方式

新增方式一：实现 Callable 接口，Callable 接口功能更加强大

- 相比 run() 方法可以有返回值
- 方法发可以抛出异常
- 支持泛型的返回值
- 需要借助 FutureTask 类，比如获取返回结果

Future 接口：

- 可以对具体 Runnable、Callable 任务的执行结果进行取消、查询是否完成、获取结果等。
- FutureTask 是 Future 接口的唯一实现类
- FutureTask 同时实现了 Runnable、Future 接口。它可以作为 Runnable 被线程执行，又可以作为 Future 得到 Callable 的返回值。

具体步骤：

```java
/**
 * 创建线程的方式三：实现Callable接口。 --- JDK 5.0新增
 *
 *
 * 如何理解实现Callable接口的方式创建多线程比实现Runnable接口创建多线程方式强大？
 * 1. call()可以有返回值的。
 * 2. call()可以抛出异常，被外面的操作捕获，获取异常的信息
 * 3. Callable是支持泛型的
 *
 * @author shkstart
 * @create 2019-02-15 下午 6:01
 */
//1.创建一个实现Callable的实现类
class NumThread implements Callable{
    //2.实现call方法，将此线程需要执行的操作声明在call()中
    @Override
    public Object call() throws Exception {
        int sum = 0;
        for (int i = 1; i <= 100; i++) {
            if(i % 2 == 0){
                System.out.println(i);
                sum += i;
            }
        }
        return sum;
    }
}


public class ThreadNew {
    public static void main(String[] args) {
        //3.创建Callable接口实现类的对象
        NumThread numThread = new NumThread();
        //4.将此Callable接口实现类的对象作为传递到FutureTask构造器中，创建FutureTask的对象
        FutureTask futureTask = new FutureTask(numThread);
        //5.将FutureTask的对象作为参数传递到Thread类的构造器中，创建Thread对象，并调用start()
        new Thread(futureTask).start();

        try {
            //6.获取Callable中call方法的返回值
            //get()返回值即为FutureTask构造器参数Callable实现类重写的call()的返回值。
            Object sum = futureTask.get();
            System.out.println("总和为：" + sum);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }

}

1. 创建一个实现 Callable 的实现类

2. 实现 call 方法，将此线程需要执行的操作声明在 call() 中。

3. 创建 Callable 接口实现类的对象。

4. 将 Callable 实现类的对象作为参数传递到 FutureTask 构造器中，创建 FutureTask 对象

5. 将 FutureTask 对象作为参数传递到 Thread 类的构造器中，创建 Thread 对象,并调用 start() 

6. 获取 Callable 中 cal() 方法的返回值 (通过调用 FutureTask 对象的 get() 获取)。<可选的>

   get() 方法的返回值即为 FutureTask 构造器参数 Callable 实现类重写的 Cal() 方法的返回值。如果不需要返回值可以在 call() 方法内部返回 null
```

> Future 接口：
>
> - 可以对具体 Runnable、Callable 任务的执行结果进行取消、查询是否完成，获取结果等
> - FutureTask 是 Future 接口的唯一实现类
> - FutureTask 同时实现了 Runnable，Future 接口。

新增方式二：使用线程池

背景：经常创建和销毁，使用特别频繁的线程，例如在并发情况下的线程，对性能影响很大

思路：提前创建好多个线程，放入线程池中，使用时直接获取，使用完放回线程池。可以比频繁创建销毁，实现重复利用。

好处：

>- 提高响应速度：(减少了创建线程的时间)
>- 降低资源消耗: (重复利用线程池中线程，不需要每次创建)
>- 便于线程管理：
> - corePolSize:核心池的大小
> - maximumPoolSize:最大线程数
> - keepAliveTime: 线程没有任务时最多保持多长时间
> - ……

Java 5.0 提供了相关线程 API：ExecutorService 和 Executor

ExecutorService: 真正的线程池接口。常见子类 ThreadPoolExecutor

> - void executor(Runnabble command):执行任务/命令，没有返回值,一般用来执行 Runnable
> - <T>Future<T>submit(Callable<T>task):执行任务，有返回值，一般用来执行 Callable
> - void shutdouwn():关闭线程池

Executors: 工具类、线程池的工厂类，用于创建并返回不同类型的线程池。

>- Executors.newCachedThreadPool():创建一个可根据需要创建新线程的线程池
>- Executors.newFixedThreadPool(
>- n):创建一个可重用固定线程数的线程池
>- Executors.newSingleThreadExecutor():创建一个只有一个线程的线程池
>- Excutors.newScheduledThreadPool(n)：创建一个线程池，它可安排在给定延迟后运行命令或定期执行。

```java
                             **
 * 创建线程的方式四：使用线程池
 *
 * 好处：
 * 1.提高响应速度（减少了创建新线程的时间）
 * 2.降低资源消耗（重复利用线程池中线程，不需要每次都创建）
 * 3.便于线程管理
 *      corePoolSize：核心池的大小
 *      maximumPoolSize：最大线程数
 *      keepAliveTime：线程没有任务时最多保持多长时间后会终止
 *
 *
 * 面试题：创建多线程有几种方式？四种！
 * @author shkstart
 * @create 2019-02-15 下午 6:30
 */

class NumberThread implements Runnable{

    @Override
    public void run() {
        for(int i = 0;i <= 100;i++){
            if(i % 2 == 0){
                System.out.println(Thread.currentThread().getName() + ": " + i);
            }
        }
    }
}

class NumberThread1 implements Runnable{

    @Override
    public void run() {
        for(int i = 0;i <= 100;i++){
            if(i % 2 != 0){
                System.out.println(Thread.currentThread().getName() + ": " + i);
            }
        }
    }
}

public class ThreadPool {

    public static void main(String[] args) {
        //1. 提供指定线程数量的线程池
        ExecutorService service = Executors.newFixedThreadPool(10);
        ThreadPoolExecutor service1 = (ThreadPoolExecutor) service;
        //设置线程池的属性
//        System.out.println(service.getClass());
//        service1.setCorePoolSize(15);
//        service1.setKeepAliveTime();


        //2.执行指定的线程的操作。需要提供实现Runnable接口或Callable接口实现类的对象
        service.execute(new NumberThread());//适合适用于Runnable
        service.execute(new NumberThread1());//适合适用于Runnable

//        service.submit(Callable callable);//适合使用于Callable
        //3.关闭连接池
        service.shutdown();
    }

}

```

### 第二章 Java 常用类

##### 1. 字符串相关的类

###### 1. String 类

Java 程序中所有的字符串的字面值 (例如："abc") 都是 String 类的实例

源码为：                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  

```java
public final class String
implements java.io.Serializable, Comparable<String>, CharSequence {
/** The value is used for character storage. */
private final char value[];
```

1.  String 被 final 所修饰，不可以被继承，value[] 被 final 修饰，代表不可变的字符序列(赋值一次之后不可以被改变)。
2.  String 接口:
    - 实现了 Serializable 接口：表示字符是支持序列化。
    - 实现了 Comparable 接口：表示 String 可以比较大小。
3.  jdk1.9 之前：String 内部定义了 final char[] value 用于存储字符串数据。jdk1.9 之后：`private final byte[] value` 使用byte[] + 字符集来存储字符串。
4.  字符串代表了不可变的字符序列。简称：不可变性
    - 体现：党对字符串进行修改(重新赋值、修改、拼接等)时，需要重新指定内存区域，而不是在原有的内存区域进行修改/赋值。
5.  通过字面量的方式给一个*字符串变量*赋值，此时，字符串值声明在声明在字符串常量池中（字符串常量池在方法区中）。
6.  **字符串常量池中是不会存储相同内容的字符串**

注意字符串常量(仅仅是一个字面值)和字符串变量的区别。

**String 不同实例化方式** ：

1. 通过字面量定义的方式。例如：`String str = "Hello";`

2. 通过 new + 构造器的方式

   ```java
   /*
       String的实例化方式：
       方式一：通过字面量定义的方式
       方式二：通过new + 构造器的方式
   
        面试题：String s = new String("abc");方式创建对象，在内存中创建了几个对象？
        两个:一个是堆空间中 new 出来的结构，另一个是 char[] value 对应的字符串常量池中的数据："abc"
   
        */ 
   @Test
       public void test2(){
           //通过字面量定义的方式：此时的s1和s2的数据javaEE声明在方法区中的字符串常量池中。
           String s1 = "javaEE";
           String s2 = "javaEE";
           //通过new + 构造器的方式:此时的s3和s4保存的地址值，是数据在堆空间中开辟空间以后对应的地址值。
           String s3 = new String("javaEE");
           String s4 = new String("javaEE");
   
           System.out.println(s1 == s2);//true
           System.out.println(s1 == s3);//false
           System.out.println(s1 == s4);//false
           System.out.println(s3 == s4);//false
   
           System.out.println("***********************");
           Person p1 = new Person("Tom",12);
           Person p2 = new Person("Tom",12);
   
           System.out.println(p1.name.equals(p2.name));//true
           System.out.println(p1.name == p2.name);//true
   
           p1.name = "Jerry";
           System.out.println(p2.name);//Tom
       }
   
   
   
   //通过字面量定义的方式：此时数据声明在方法区的字符串常量池中。
   String str = "abc";
   String str1 = "abc";
   System.out.println(str == str1);//true
   //通过 new + 构造器的方式：此时数据声明在堆中。
   String str2 = new String("abc");
   String str3 = new String("abc");
   System.out.println(str2 == str3);//false
   
   //this.value = new char[0];
   String s1 = new String();
   //this.value = original.value;
   String s2 = new String(String original);
   //this.value = Arrays.copyOf(value, value.length);
   String s3 = new String(char[] a);
   String s4 = new String(char[] a,int startIndex,int count);
   注释：注释内容,可以将其看作为构造器内部的逻辑
   ```

通过字面值的方式创建字符串和 new 的方式创建字符串 value 的不同。

- 通过字面值的方式创建的字符串，变量中存储的是 char[] 在字符常量池中的首地址
- 而通过 new 的方式创建的字符，变量中存储的是 value 的地址，而value 中存储的才是 char[] 在字符串常量池中的地址。

面试题：String s = new String("abc");方式创建对象，在内存中创建了几个对象？

两个:一个是堆空间中new结构，另一个是char[]对应的常量池中的数据："abc"

**String 不同拼接操作的对比:**

```java
     	String s1 = "JavaEE";
        String s2 = "hadhoop";

        String s3 = "JavvEEhadhoop";
        String s4 = "JavaEE" + "hadhoop";
        String s5 = s1 + "hadhoop";
        String s6 = "JavaEE" + s2;
        String s7 = s1 + s2;
        String s8 = "JavaEE" + s2;

        System.out.println(s3 == s4);//true
        System.out.println(s3 == s5);//false
        System.out.println(s3 == s6);//false
        System.out.println(s5 == s6);//false
        System.out.println(s5 == s7);//false
        System.out.println(s6 == s7);//false
        System.out.println(s3 == s7);//false
        System.out.println(s6 == s8);//false
//创建在堆中的数据可以重复，
_______________________________
String s9 = s6.intern();//返回值得到的s8使用的常量值中已经存在的“javaEEhadoop”
System.out.println(s3 == s9);//true
_________________________
String s1 = "JavaEE" + "hadoop";
final String s4 = "JavaEE";//s4 常量
String s5 = s4 + "hadoop"; 
System.out.println(s1 == s5);//true
注意：因为我们用 final 修饰 s4,所以 s4 是一个常量，而常量与常量的拼接的结果在常量池中，因此 s1 == s4
```

结论:

>- 字面量字符串拼接的结果在常量池中。（常量与常量的拼接结果在常量池中）
>- 在拼接中，只要有一个是变量 (该变量不是常量)，结果就在堆中。
>- 如果拼接的结果 (或字符串变量)调用 intern() 方法，返回值就在常量池中。

面试题1：

```java
public class StringTest {

    String str = new String("good");
    char[] ch = { 't', 'e', 's', 't' };

    public void change(String str, char ch[]) {
        str = "test ok";
        ch[0] = 'b';
    }
    public static void main(String[] args) {
        StringTest ex = new StringTest();
        ex.change(ex.str, ex.ch);
        System.out.println(ex.str);//good
        System.out.println(ex.ch);//best
    }
}
//方法形参传递的是地址，我们在方法中对该内存区域进行重新赋值，由于 value 或字符串的字面值的不可变性，会重新对其指定一个内存区域给 str。也就说方法内 str 的地址值已经发生了改变。而方法外 str 的地址值并没有发生改变，因此在change() 外，str 的值并没有发生改变。
//这里的方法形参就是取出 str 的地址值，然后根据将其赋值给方法形参的 str 值。即方法内外的实参和形参是两个完全不同的体系。
注：注意 string 和其它引用数据类型的区别，如果是其它引用数据类型，在方法中对齐进行重新赋值，那么将会对原有的数据类型进行覆盖。
例如：
@Test
public void test3(){
    Person p1 = new Person(12,"Tom");
    System.out.println(p1.getName());//Tom
}

public class Person {

    private int age;
    private String name;

    public Person() {
    }

    public Person(int age, String name) {
        this.age = age;
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public String getName() {
        return name;
    }
    public void change(Person person){
        person = new Person(34,"jerry");
    }
}
从这里我们可以发现：方法内的形参和方法外的实参是完全独立的。
总结：在方法中字符串进行修改和在方法对引用数据类型重新赋值原理一致。
```

画图理解：

![Snipaste_2020-06-27_15-44-05](C:/doc/Markdown文档/Java/Snipaste_2020-06-27_15-44-05.png)

###### 2. String 的常用方法：



**JVM 中涉及字符串的内存结构**

三种JVM

>- Sun 的 HotSpot //被 Oracle 收购
>- Bea 的 JRockit  //被 JRockit 收购
>- IBM 的 J9 VM

**堆 Heap**

我们可以将堆划分三部分：

>- Young Generation Space新生区
>- Tenure Generation Space 养老区
>- *Permanent Space 永久存储区*
>
>一个 JVM 实例只会存在一个堆内存，堆内存的大小是可以调节的。

**新生区：**

新生区是类的诞生，成长、消亡的区域，一个类在这里产生，应用，最后被垃圾回收收集，结束生命。新生区又分为两个部分：伊甸区 (Eden Space) 和幸存者区（Survivor Spce），所有的类(这里应该是 "对象" 吧)多是在伊甸区被 new 出来的。幸存区又两个 0区 （0 space）和 1区 (1 space)。当伊甸区的空间用完，而程序又需要新建对象时，JVM 会对伊甸区进行垃圾回收(将伊甸区中不被其它对象引用的对象进行销毁)/。然后将伊甸区中剩余的对象移动到幸存 0区，若幸存 0区也满了，再次对该区进行垃圾回收，然后移动到 1区，若 1区也满了 ……移动到 养老区，若养老区也满了，那么这个时候将产生 Major GC (FullGC),进行养老区的内存清理，若养老区执行了 Full GC 之后，发现依然无法进行对象的保存，就会产生 OOM 异常。

*如果出现 java.lang.OutOfMemoryError:java heap space 异常，说明 JVM 的对内存不够：原因有二：*

1. *Java 虚拟机的堆内存设置不够，可以通过参数 -Xms、-Xmx 来调整*
2. *代码中创建了大量对象，并且长时间不能被垃圾收集器收集。——内存溢出；内存泄漏*

**永久存储区(方法区)：**

永久存储区是一个常驻内存区域，用于存放 JDK 自身所携带的 class、interface 的，也就是说它存储的时运行环境的必须的类信息，被装载进此区域的信息是不会被垃圾回收器回收掉，关闭 JVM 才会释放此区域所占用的内存。

*如果出现 java.lang.OutOfMemoryError: PermGen space,说明 JVM 对永久区内存设置不够。一般出现这种情况，都是程序启动需要加载大量第三方的 Jar 包*

*例如：在一个 Tomcat 下部署了太多的应用，或大量动态反射生成的类被不断加载，最终导致 Perm 区被占满。*

各个版本常量池的位置：

>JDK 1.6 之前：常量池分配在“”，1.6 在方法区
>
>JDK 1.7 ：有，但已经逐步“”
>
>JDK 1.8 ：无，1.8 在元空间

方法区 (Method Area) 和堆一样，是各个线程共享的内存区域，它用于存储虚拟机加载：类信息、普通常量静态变量、编译器编译后的代码等等。

- 虽然 JVM 规范将方法区描述为堆的一个逻辑部分，但它还有一个别名(non_heap) 非堆，就是要和堆分开。

- 对于 HotSpot 虚拟机， 很多开发者习惯将其称之为 “永久代”，但严格本质上来说两者不同，或者使用永久代实现方法区而已，JDK1.7 版本中，已经将原本放在永久代的字符常量池移走。
- 常量池是方区的一部分，Class 文件除了有类的版本，字段、方法、接口等描述信息之外，还有一项信息就是常量池，这部分内容将在类加载后进入方法区运行时常量池存放。

**String 常用的方法：**

| int length();                                  | 返回指定字符串的长度                                         |
| ---------------------------------------------- | ------------------------------------------------------------ |
| char charAt(int index)                         | 返回指定索引处的字符                                         |
| boolean isEmpty()                              | 判断是否是空字符串：renturn value.length == 0;               |
| String toLowerCase()                           | 将 String 中所有字符转换为小写                               |
| String toUpperCase()                           | 将 String 中所有字符转换为大写                               |
| String trim()                                  | 返回字符串的副本，忽略前置空白和尾部空白。                   |
| Boolean equals(Object obj)                     | 比较字符串的内容是否相同                                     |
| String equalsIgnoreCase(String anotherString)  | 同 equals(),忽略大小写。                                     |
| String concat(String str)                      | 将指定字符串连接到此字符串的结尾用 concat()                  |
| int compareTo(String anotherSString)           | 比较两个字符串的大小.//返回负数为当前字符串小，正数当前对象大，0 相等 |
| String substring(int endIndex);                | 返回一个新的字符串，它是此字符串从 beginIndex 开始截取       |
| String substring(int beginIndex，int endIndex) | 返回一个新的字符串，它是从此字符串 beginIndex 开始到 endIndex 结束//[ ) |
| boolean endsWith(String suffix)                | 测试此字符串是否以自定的后缀结尾                             |
| boolean startsWith(String prefix)              | 测试此字符串是否以指定的前缀开始                             |
| boolean srartsWith(String prefix,int toffset)  | 测试字符串从指定索引开始的子字符串，是否以指定的前缀开始，   |
| boolean contains(CharSqurence s )              | C当且仅当此字符串包含指定的 char 值序列                      |
| int indexOf(String str)                        | 返回指定字符串在此字符串第一次出现的位置                     |
| int indexOf(String str,int fromIndex)          | 返回，从指定索引开始，返回指定子字符串在此字符串中第一次出现的位置 |
| int lastIndexOf(String str,int formindex)      | 返回指定字符串在此字符串中最后一次出现的索引位置             |
| int lastIndexOf(String index,int formIndex)    | 返回指定字符串在此字符串中最后一次出现的索引位置(从指定的索引位置反向索引,也就是从后往前找) |

index 和 indexOf 方法如果未找到返回都是 - 1

在什么情况下 indexof(str) 和 lastIndexof(str) 的返回值相同？

存在一个唯一的 str 或 此字符串中根本没有 str

| 方法名称及格式                                               | 介绍                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| String repacle(char oldChar,char newChar)                    | 返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所有的 oldChar //**替换一个字符** |
| String replace(CharSequence target,CharSequence replecement) | 使用指定的字面值替换序列替换此字符串中所有匹配子面子字符序列的子字符串//**替换一个字符串**i |
| Strint replaceAll(String regex, String replacemement)        | 使用给定的 replacemement 替换此字符串中所有匹配给定的正则表达式的子字符串 |
| String replaceFirst(String regex,String replacemement)       | 使用给定的 replacemenent 替换此字符串中匹配给定的正则正则表达式的第一个字符串 |
| booolean matchex(String regex)                               | 告知字符串是否存在匹配给定的正则表达式                       |
| String[] split(String regex)                                 | 根据给定的正则表达式拆分此字符串                             |
| String[] split(String regex,int limit)                       | 根据匹配的正则表达式来拆分此字符串，最多不超过 limit 个，如果超过了剩下的全部放到最后一个元素中。 |

###### 3. 字符串与其它结构之间的转换

**String 与 char[] 之间的转换：**

- String --> char[]: 调用 String 的 toCharArray()

- char[] --> String: 调用 String 的构造器

```
String str = "abc123";
char[] c = String.toCharArray(abc123);
String str = new String(c);
```

**字符串和字节数组之间的转换：**

1\. *字节数组  --> 字符串* 

调用 String 的构造器

- String(byte[]):通过使用平台默认字符集解码指定的 byte 数组，构造一个新的 String
- String(byte[],String charsetName):使用指定的字符集解码byte 数组，构造一个新的 String
- String(byte[],int offset,int length)：用指定字节数组的一部分，即从数组的起始位置 offset 开始取 length 个字节构成一个字符串对象。

2\. 字符串 --> 字节数组

调用 String 的 getBytes() 方法

- public byte[] getBytes():使用平台默认的字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中

- public byte[] getBytes(String,charsetName): 使用指定的字符集将此 String 编码为 byte 序列，并将结果存到一个新的 byte 数组中。

```java
String str = "abc123";
byte[] bytes = str.getBytes();//使用默认的字符集进行编码
System.out.println(Array.toString(bytes));
byte[] byte1 = str.getBytes("gbk");//使用 gbk 字符集进行
```

**编码与解码：**

- 编码：字符串 -->字节  (看得懂 --->看不懂的二进制数据)

- 解码：编码的逆过程，字节 --> 字符串 （看不懂的二进制数据 ---> 看得懂）

  说明：解码时，要求解码使用的字符集必须与编码时使用的字符集一致，否则会出现乱码。

编码：将我们能够看懂的字符转换为看不懂的二进制数据

解码：将看不懂的二进制数转换为我们能看懂的字符

###### 4. StringBuffer 可变的字符序列

使用 char[] value 进行存储。value 没有 final 修饰，value 可以不断扩容

构造器：

- StringBuffer()：初始容量为16的字符串缓冲区
- StringBuffer(int size)：构造指定容量的字符串缓冲区
- StringBuffer(String str)：将内容初始化为指定字符串内容;容量为 str.length + 16

StringBuffer、StringBuilder

>String、StringBuffer、StringBuilder 三者的区别：
>
>**相同点**：底层结构使用 char[] 进行存储
>
>**不同点**：
>
>- String: 不可变的字符序列；
>
>- StringBuffer:可变的字符序列：线程安全的，效率低
>
>- StringBuilder: 可变的字符序列：JDK 5.0 新增，效率高，线程是不安全的

**问题一**：

```java
StringBuffer sb1 = new StringBuffer("abc");
System.out.println(sb1.length);//3
```

**问题二**：

扩容问题：如果要添加的数据底层数组盛放不下，那就需要扩容，默认情况下，扩容为原来的 2 倍 + 2，同时将原有数组中的元素复制到新的数组中。如果要添加的数据过大，扩容为来的 2 倍 + 2，还不足以盛放数据，那么就需要，将数组的长度扩充为该数据的长度，同时将原有数组中的元素复制到新的数组中。

指导：开发中建议使用；StringBuffer(int capacity);

**StringBuffer 类常用的方法**:

- StringBuffer append(xxx)：提供了很多的 append() 方法，用于进行字符串拼接

- StringBuffer delete(int start,int end):删除指定位置的内容

- StringBuffer replace(int start,int end,String str):把 [start,end） 位置替换为 str

- StringBuffer insert(int offset,xxx): 在指定位置插入 xxx

- StringBuffer reverse(): 把当前字符序列逆转

以下方法与 String 中的方法一致：

public int  indexOf():

public String substring(int start,int end):返回一个所引从 start 开始到 end 结束左闭右开区间的子字符串

public int length():
public char charAt(int n):

public void setCharAt(int n,char ch):

总结：

>增：append(xxx);
>
>删：delete(xxx);
>
>改：setCharAt(int n,char ch)
>
>查：charAt(int n)
>
>插：insert(int offset,xxx)
>
>长度：length
>
>遍历：for() + charAt()/toString;

- 当 append 和 insert 时，如果原来的 value 数组长度不够，可扩容。

- 如上这些方法自己支持方法链操作

- 方法链的原理：

  ```java
  @Test
  public StringBuffer append(String str){
      super.appdend(str);
      return this;
  }
  ```

String、StringBuffer、StringBuilder 三者之间效率的对比：

效率从高到低： StringBuilder、StringBuffer、String

##### 2. JDk 8 之前的时间日期 API

> **1.Java.lang.System 类**
>
> `public static long currentTimeMills();`
>
> 返回当前时间与 1970 年 1 月 1 日 0 时 0 分 0 秒之间的时间差(精确到毫秒)。称之为*时间戳*。
>
> 此方法适用于计算时间差。
>
> 计算时间的主要标准：
>
> > - UTC	（Coordinated Universal Time）
> > - GMT （Grreenwich Mean Time）
> > - CST （Central Strandard Time）
>
> **2. java.util.Date 类**
>
> Java.util.Date 
>
> |——java.sql.Date 
>
> 表示特定的时间，精确到毫秒
>
> 1. 两个构造器的使用
>
>    - Date()： 创建一个对应当前时间的 Date  对象
>    - Date(long date):创建指定毫秒数的 Date 对象
>
> 2. 两个方法的使用
>
>    - toString(): 显式当前的年、月、日、时、分、秒；
>    - getTime(): 获取当前 Date 对象对应的毫秒数
>
> 3. java.sql.date 对应着数据库中的日期类型的变量
>
> 4. 将 java.util.Date 的对象，转换为 Java.sql.Date;
>
>    情况一：
>
>    ```java
>    Date date = new java.sql.Date(234234234L);
>    Java.util.Date = (java.util.Date)date;
>    ```
>
>    情况二：
>
>    ```java
>    Date date1 = new Date();
>    java.sql.Date date2 = new Java.sql.Date(date1.getTime());
>    ```

**SimpleDateFormat 类**

java.text.SimpleDateFormat 类

1. System 类中 currentTimeMillis();
2. java.util.Date 和 java.sql.Date
3. SimpleDateFormat
4. Calender

>- Date 类的 API，不易于国际化，大部分都废弃了，SimpleDateFormat 类是一个不与语言环境有关的方式来格式化和解析时间的具体类
>
>格式化: 日期 ----> 字符串
>
>- SimpleDateFormat(): 默认的模式和语言环境创建对象
>- public SimpleDateFormat(String pattern): 该构造方法可以用参数 pattern 指定的格式创建一个对象，该对象调用
>- public String format(Date date): 方法格式化时间对象  Date
>
>解析:格式化的逆过程   字符串 ---->  时间
>
>- public Date parse(Stirng source): 从给定的字符串开始解析文本，以生成一个日期
>
>```java
>SimpleDateFormat sdf  = new SimpleDateFormat();
>//
>String format  = sdf.format(date);
>System.out.println(format); // “19-2-18 上午12：48”
>
>//解析
>String str = "19-12-18 上午12:12:12"; //只能解析这种格式
>Date date =  sdf.parse(str);
>Date date = sdf.parese(str);
>System.out.println(date1);
>//使用指定方式格式化和解析：调用带参的构造器
>SimpleDateFormat sdf 1= new SimpleDateFormat(“yyyy-MM-dd  hh:mm:ss”);
>//详见 Java 文档
>String format1 = sdf1.
>```

**Calender 日历类(抽象类)的使用**

实例化

方式一：创建其子类 GregorianCalendar 的对象

方式二：调用其静态方法 getInstance()

```java
Calendar calendar = Calendar.getInstance();
int days = calendar.get(Calendar.DAY_OF_MONTH);
```

常用方法：

一个 Calendar 的实例时系统时间的抽象表示，通过 get(int field)方法来获取想要的时间信息。比如 YEAR、MONTH、DAY_OF_MONTH、DAY_OF_YEAR、DAY_OF_WEEK、MINUTE、SECOND

| get()                                  | 获取一些常用的属性信息<详见 Java 文档> |
| -------------------------------------- | -------------------------------------- |
| public void set(int field,int value)   | 设置属性                               |
| public void add(int field,int amount)  | 堆属性进行添加                         |
| public final date getTime():           | 日历类 --> Date                        |
| public final void set Time(Date date): | date --> 日历类                        |

注意：

- 获取月份时，一月是 0，二月是 1，……，十二月是 11
- 获取星期时：周日是 1，周一是 2，…… 周六是 7

##### 3. JDK 8 中新的时间日期 API

JDK 1.0 包含了 java.util.Date 类，但是它的大多数方法都已经在 JDK 1.1 引入 Calendar 类之后，被启用了。而 Calendar 面临的问题是：

- 可变性：像日期和时间这样的类应该是不变的。

- 偏移性：Date 中的年份是从 1990 年开始，而月份都从 0 开始。

- 格式化：格式化只对 Date 有用，而 Calendar 则不行

  此外，它们也不是线程安全的；不能处理闰秒

闰秒：由于地球自转的不均匀性和长期变慢性（主要由潮汐摩擦引起的），会使世界时（民用时）和原子时之间相差超过 &pm;0.9 秒时

- 在 JDK 8 中引入 java.Time  API 已经纠正了过去的缺陷，将来很长一段时间，它都会为我们服务。
- Java 8 吸收了 Joda-Time 的精华，以一个新的开始为 Java 8 创建优秀的 API。新的 java.time 包含了所有关于本地日期（LocalDate）、本地时间（LocalTime）、本地日期时间（LocalDateTime）、时区（ZonedDateTime）和持续时间（Duration）的类。历史悠久的 Date 类新增了 toInstant 方法，用于把 Date 转换成 新的表示形式，这些新增的本地化时间日期 API 大大简化了日期时间和本地化的管理。

>java.time - 包含值对象的基础包
>
>java.time.chrono - 提供对不同日历系统的访问
>
>java.time.format - 格式化和解析时间和时间
>
>java.time.temporal - 包括底层框架和扩展性
>
>java.time.zone - 包含时区支持的类

说明：大多数开发者只会用到基础包和 format 包，也可会用到 temporal 包。因此尽管有 68 个新的公开类型，大多数开发者，大概将只会用到其中的三分之一。

LocalDate、LocalTime、LocalDateTime

说明：

1. LocalDateTime 相较于 LocalDate、LocalTime 使用频率更高。

|                             方法                             | 描述                                                         |
| :----------------------------------------------------------: | :----------------------------------------------------------- |
|                  now() /* now(Zoneld zone)                   | 静态方法，根据当前时间创建对象/指定时区创建对象（获取当前的时间 \ 日期 \ 时间 + 日期） |
|                             of()                             | 静态方法//设置指定年、月、日、时、分、秒，没有偏移量         |
|                getDayOfMonth()/getDayOfYear()                | 获得月份天数(1 ~ 31)/获得年份天数(1~366)                     |
|                       getDayOfWeek();                        | 获得星期几(返回一个 DayofWeek 枚举值)                        |
|                          getMonth()                          | 获得月份，返回一个 Month 枚举值                              |
|                  getMonthValue()/getYear()                   | 获得月份（1~12）/获得年份                                    |
|              getHour()/getMinute()/getSecond()               | 获得当前对象对应的小时、分钟秒                               |
|   withDayOfMonth()/withDayOfYear()/withMonth()/withYear()    | 将月份、年份天数、月份、年份修改为指定的值，并返回新的对象   |
| plusDays()/plusWeeks()/plusMonths()/plusYears()/plusHours()… | 向当前对象添加几天、几周】几个月、几年、几小时…              |
| minusDays()/minusWeeks()/minusMonths()/minusYears()/minusHours()… | 向当前对象减少几天、几周、几个月、几年、几小时…              |
|                                                              |                                                              |

getXXX(); 获取相关的属性

wihtXXX();设置相关的属性 //体现不可变形，有返回值  

例子：

```java
LocalDateTime localDateTime = LocalDateTime.now();
LocalDateTime localDateTime = LocalDateTime.of(参数列表);

```

**Instant**

> - Instant 是时间线上的一个瞬时点，这可能被用来记录，应用程序中的事件时间戳。
> - 在处理时间时我们会想到年、月、日、时、分、秒。然而这只是时间的一个模型，是面向人类的。第二种通用模型是面向机器的，或者说是连续的。在此模型中，时间线中的一个点表示为一个很大的数，这有利于计算处理。在 UNIX 中，这个数是从 1970 年开始，以秒为单位。在 Java 中，这个数也是从 1970 年开始，但是以毫秒为单位
> - java.time 包通过值类型 Instant 提供机器识图。Instant 表示为时间线上的一个点，不需要任何的上下文信息，例如：时区，从概念上来讲，它只是简单的表示从 1970 年 1 月 1 日 0 时 0 分 0 秒（UTC）开始的秒数。因为 java.time 包是基于纳秒计算的，所以 Instant 的精度可以达到纳秒级。
> - 1 ns = $10^{-9} s\  \ \ 1秒= 10^3毫秒=10^6微秒=10^9纳秒$  



| 方法                          | 描述                                          |
| ----------------------------- | --------------------------------------------- |
| now()                         | 静态方法，获取本初子午线对应的标准时间        |
| atOfferSet(ZoneOffset offset) | 结合及时的偏移来创建一个 offsetDateTime       |
| toEpochMilli()                | 获取对应的毫秒数                              |
| ofEpochMilli(long epochMilli) | 静态方法，通过给定的毫秒数创建 Instant 实例。 |

DateTimeFormatter

>java.time.format.DateTimeFormatter 类：该类提供三种格式化的方法：
>
>- 预定义的标准格式：
>
>ISO_LOCAL_DATE_TIME;ISO_LOCAL_DATE;ISO_LOCAL_TIME;
>
>- 本地化相关的格式:如：ofLocalizedDateTime(FormatStyle.LONG)
>
>FormatStyle.LONG/FormatSystle.MEDIUM/FormatStyle.SHORT 使用 ofLocalizedDateTime
>
>- 自定义的格式。如：ofPattern("yyyy-MM-dd hh:mm:ss E")
>
>FormatStyle.FULL/FormatStyle.LONG/FormatSystle.MEDIUM/FormatStyle.SHORT

| 方法                       | 描述                                                 |
| -------------------------- | ---------------------------------------------------- |
| ofPattern(String pattern)  | 静态方法，返回一个指定字符串格式的 DateTimeFormatter |
| format(TemporalAccessor t) | 格式化一个日期、时间、返回字符串                     |
| parse(CharSequence text)   | 将指定格式的字符序列，解析为一个时间日期             |

##### 4. Java 比较器

在 Java 中经常会涉及到容器中对象的排序问题，那么就涉及到容器中对象的比较问题。

> 说明：Java 中的对象，正常情况下，只能进行比较 == 或 !=,不能使用 < 或 >。 但是在实际开发场景中，我们需要对多个对象进行排序（比较对象），如何实现？使用两个接口中的任意一个 comparable 或 comparator

Java 实现对象排序的方式有两种：

- 自然排序：java.lang.camparable

  使用：

  1. 像 String、包装类等实现了 Comparable 接口，重写了 compareTo(obj) 方法，进行从小到大的顺序进行排列。

  2. 重写 compareTo(obj) 的原则：

     如果当前对象 this 大于形参对象 obj,则返回正整数。

     如果当前对象 this 小于形参对象 obj,则返回负整数

     如果当前对象 this 等于形参对象 obj,则返回零。

  3. 对于自定义类来说，如果需要排序，我们可以让自定义类来实现 comparable 接口，重写 compareTo(obj) 方法，在 compareTo() 方法中指明如何排序。

**定制排序：java.lang.coparator**

- 当元素没有实现 java.lang.comparable 接口而又不方便修改代码，或者实现了 java.lang.comparable 接口的排序规则不适合当前操作。那么可以考虑使用 comparator 对象来排序，强行对多个对象进行整体的排序比较

- 重写 compare(Object o1,Obje o2) 比较 o1 和 o2 的大小：

  如果返回正整数，则表示 o1 > o2

  如果返回负整数，则表示 o1 < o2

  如果返回 0，则表示 o1 == o2;

- 可以将 Comparator 传递给 sort 方法 (如 collections.sort() 或 Arrays.sort()),从而允许在排序顺序上实现精准控制

- 还可以使用 Comparator 来控制某些数据结构（如有序 set 和有序映射）的顺序，或者为哪些没有自然顺序的对象 collection 提供顺序。

举例：

```java
//没有使用泛型，按照从小到大的顺序排列
String[] strArr = new String[]{"aa","bb","gg","cc","DD","aa"};
Arrays.sort(strArr,new Comparator(){
    @Override 
    public int compare(Object o1,Object o2){
        if(o1 instanceof String && o2 instanceof String){
            String str1 = (String)o1;
            String str2 = (String)o2;
            return -str1.compareTo(str2);
        }
        throw new RuntimeException("输入的数据类型有误");
        
    }
    
});
//使用泛型
  Arrays.sort(strArr, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return -o1.compareTo(o2);
            }
  });
```

compareble 接口的使用与 comparator 使用的对比：

comparable 接口的方式一旦指定，保证 Comparable 接口实现类的对象在任何位置都可以比较大小。

comparator 接口属于临时性的比较。

##### 5. System 类

> - System 类代表系统，系统几的很多属性和控制方法，都放置在该类的内部，该类位于 java.lang 包。
>
> - 由于该类的构造器是 private 的，所以无法创建该类的对象，也就无法实例化该类。其内部的成员变量和成员方法都是 staitc 的，可以很方便的进行调用。
>
> - 成员变量
>
>   System 类内部包含 in、out 和 err 三个成员变量，分别代表输入流 in (输入流),标准输出流（显示器）和标准错误输出流（显示器）
>
> - 成员方法
>
>   - native long currentTimeMillis();
>
>     该方法返回当前的计算机时间，时间的表达式为当前计算机时间和 GMT 时间(格林威治时间) 1970 年 1 月 1 号 0 时 0 分 0 秒的所差的毫秒数
>
>   - void exit(int status);
>
>     该方法的作用是退出程序。其中 status 的值为 0 代表正常退出，非零则代表异常退出。使用该方法可以在图形交界面编程中实现程序的退出功能。
>
>   - void gc();
>
>     该方法的作用时请求系统进行来及回收，至于系统是否会立刻进行垃圾回收，则取决于系统中垃圾回收算法的实现以及系统执行时的情况。
>
>   - String getProperty(String key)
>
>     该方法的作用时获得系统中属性名为 key 的值。 系统中常见的属性名及属性的作用如下表所示。
>
>   | 属性名       | 属性作用            |
>   | ------------ | ------------------- |
>   | java.version | Java 运行时环境版本 |
>   | java.home    | Java 安装目录       |
>   | os.name      | 操作系统的名称      |
>   | os.version   | 操作系统的版本      |
>   | user.name    | 用户的账户名称      |
>   | user.home    | 用户的主目录        |
>   | user.dir     | 用户的当前工作目录  |

##### 6. Math类

> Java.lang.Math 提供了一系列*静态*方法用于科学计算，其方法的参数和返回值一般为 double 类型。
>
> - abs 绝对值
>
> - acos,asin,atan,cos,sin,tan 三角函数
> - sqrt 平方根
> - pow(double a,double b) 
> - log 自然对数
> - exp e 为底指数
> - max(double a,double b)
> - min(double a,double b)
> - random 返回 0.0 到 1.1 的随机数
> - long round(double a) double 型数 a 转换为 long 型(四舍五入)
> - toDegrees(double angrad) 弧度 ——> 角度
> - toRadians(double angdeg) 角度 ——> 弧度

##### 7. BigInteger 和 BigDecimal

>- Integer 类作为 int 的包装类，能存储的最大整型值为 $ 2^{31}-1$,Long 类也是有限的，最大为 $2^{63}-1$。如果表示再大的整数不管时基本数据类型还是包装类它们都无能为力。更不用说进行运算了
>- java.math 包的 BigInteger 可以表示不可变的任意精度的整数，BigInteger 提供所有 Java 的基本数据操作符的对应物，并提供 java.lang.Math。所有相关方法。另外，BigInteger 还提供以下运算：模算术、GCD 计算、素数生成、位操作、以及其它一些操作。
>
>- 构造器
>
>BigInteger(String val); 根据字符串构建 BigInteger 对象
>
>常用方法：
>
>- public BigInteger abs() 返回此 BigInteger 的绝对值的 BigInteger
>- BigInteger add(BigInteger val)；返回其值为 (this + val) 的 BigInteger
>- BigInteger subtract(BigInteger val);返回其值为 (this - val) 的 BigInteger
>- BigInteger multiply(BigInteger val);返回其值为 (this * val) 的 BigInteger
>- BigInteger divide(BigInteger val);返回其值为 (this / val) 的 BigInteger。整数相除只保留整数部分
>- BigInteger remainder(BigInteger val) 返回值为(this % val) 的 BigInteger
>- BigInteger[] divideAndRemainder(BigInteger val);返回其值为 (this / val) 的 BigInteger 后跟(this % val) 两个 BigInteger 的数组
>- BigInteger pow(int exponent): 返回值为(this^exponent^) 的 BigInteger

**BigDecimal**

>- 一般的 Float 类和 Double 类 可以用来做科学计算或工程计算。但在商业计算中，要求数字精度较高，故用到 java.math.BigDecimal 类
>
>- 构造器
>
>public BigDecimal(double val);
>
>public BigDecimal(String val);
>
>- 常用方法：
>
>public BigDecimal add(BigDecimal augend)
>
>public BigDecimal subtract(BigDecimal subtrahend)
>
>public BigDecimal multiply(BigDecimal multiplicaned)
>
>public BigDecimal divide(BigDecimal divisor,int scale,int roundingMode) ;scal 为精度位；BigDecimal.ROUND_HALF_UP;

### 第三章 枚举类型与注解

##### 1. 枚举类的使用

> - 类的对象只有有限个，确定的。
> - 当需要定义一组常量时，强烈建议使用枚举类。
> - 如果枚举类只有一个对象，可以作为单例模式的实现

如何自定义枚举类：

> 方式一：JDK 5.0 之前，自定义枚举类
>
> 1. 声明对象的属性要用 private final 修饰。
>
> 2. 私有化类的构造器，并给属性赋值。
> 3. 提供当前枚举类的多个对象：public static final 修饰
> 4. 其它诉求：获取对象的属性
> 5. 提供 toString() 方法。

JDK 5.0 如何使用关键字 enum 定义枚举类：

> 1. 提供当前枚举类的对象，多个对象之间用逗号隔开，末尾对象用分号结束。
> 2. 私有化对象的属性用 private final 修饰
> 3. 私有化类的构造器并对象属性赋值
>
> Tips：这个顺序不可以调换

JDK 1.5 之前

```java
public interface Info{
    void show();
}
//自定义枚举类
class Season{
    //1.声明Season对象的属性:private final修饰
    private final String seasonName;
    private final String seasonDesc;

    //2.私有化类的构造器,并给对象属性赋值
    private Season(String seasonName,String seasonDesc){
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }

    //3.提供当前枚举类的多个对象：public static final的
    public static final Season SPRING = new Season("春天","春暖花开");
    public static final Season SUMMER = new Season("夏天","夏日炎炎");
    public static final Season AUTUMN = new Season("秋天","秋高气爽");
    public static final Season WINTER = new Season("冬天","冰天雪地");

    //4.其他诉求1：获取枚举类对象的属性
    public String getSeasonName() {
        return seasonName;
    }

    public String getSeasonDesc() {
        return seasonDesc;
    }
    //4.其他诉求1：提供toString()
    @Override
    public String toString() {
        return "Season{" +
                "seasonName='" + seasonName + '\'' +
                ", seasonDesc='" + seasonDesc + '\'' +
                '}';
    }
}

public class SeasonTest {

    public static void main(String[] args) {
        
        Season spring = Season.SPRING;
        System.out.println(spring);

    }

}
```

JDK 5.0

```java
//使用enum关键字枚举类
enum Season1 implements Info{
    //1.提供当前枚举类的对象，多个对象之间用","隔开，末尾对象";"结束
    SPRING("春天","春暖花开"){
        @Override
        public void show() {
            System.out.println("春天在哪里？");
        }
    },
    SUMMER("夏天","夏日炎炎"){
        @Override
        public void show() {
            System.out.println("宁夏");
        }
    },
    AUTUMN("秋天","秋高气爽"){
        @Override
        public void show() {
            System.out.println("秋天不回来");
        }
    },
    WINTER("冬天","冰天雪地"){
        @Override
        public void show() {
            System.out.println("大约在冬季");
        }
    };

    //2.声明Season对象的属性:private final修饰
    private final String seasonName;
    private final String seasonDesc;

    //2.私有化类的构造器,并给对象属性赋值

    private Season1(String seasonName,String seasonDesc){
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }

    //4.其他诉求1：获取枚举类对象的属性
    public String getSeasonName() {
        return seasonName;
    }

    public String getSeasonDesc() {
        return seasonDesc;
    }
//    //4.其他诉求1：提供toString()
//
//    @Override
//    public String toString() {
//        return "Season1{" +
//                "seasonName='" + seasonName + '\'' +
//                ", seasonDesc='" + seasonDesc + '\'' +
//                '}';
//    }

// 统一实现该方法
//    @Override
//    public void show() {
//        System.out.println("这是一个季节");
//    }
}



 public static void main(String[] args) {
        Season1 summer = Season1.SUMMER;
        //toString():返回枚举类对象的名称
        System.out.println(summer.toString());

//        System.out.println(Season1.class.getSuperclass());
        System.out.println("****************");
        //values():返回所有的枚举类对象构成的数组
        Season1[] values = Season1.values();
        for(int i = 0;i < values.length;i++){
            System.out.println(values[i]);
            values[i].show();
        }
        System.out.println("****************");
        //获取 State 中的枚举常量
        Thread.State[] values1 = Thread.State.values();
        for (int i = 0; i < values1.length; i++) {
            System.out.println(values1[i]);
        }

        //valueOf(String objName):返回枚举类中对象名是objName的对象。
        Season1 winter = Season1.valueOf("WINTER");
        //如果没有objName的枚举类对象，则抛异常：IllegalArgumentException
//        Season1 winter = Season1.valueOf("WINTER1");
        System.out.println(winter);
        winter.show();
```

Enum 类的主要方法：

![image-20210310232140733](C:/doc/Markdown文档/Java/image-20210310232140733.png)

> - values() 方法：静态方法，返回枚举类型的对象数组
> - valueof(String str ); 静态方法，返回枚举类型对象名是 str 的对象，如果名为 str 的枚举类的对象没有找到，则抛出异常 IllegalArgumentException
> - toString(); 返回当前枚举类对象常量的名称

- 和普通 Java 类一样，枚举类可以实现一个或多个接口
- 若每个枚举值在调用实现的接口方法呈现相同的行为方式，则只要统一实现该方法即可。
- 若需要每个枚举值在调用实现的接口方法呈现出不同的行为方式,  则可以让每个枚举值分别来实现该方法

**使用 Enum 关键字定义的枚举类实现接口：**

情况一：实现接口，在 enum 类中实现抽象方法

情况二：让我们的枚举类的每个对象分别去实现接口中的抽象方法。

格式为：对象名称(参数列表){ 重写的方法{要执行的操作} }

##### 2. 注解(Annotation)的使用

- 注解的使用

- 常见的注解示例

- 自定义注解

- JDK 中的元注解

- 利用反射获取注解信息（在反射部分涉及）

- JDK 8 中注解的新特性

  +++

- 从JDK 5.0 开始，Java 增加了对元数据 (MetaData) 的支持，也就是 Annotation （注解）

- Annotation 其实就是代码中的特殊标记(例如：@ovrride,@author……)，这些标记可以在编译、类加载、运行时被读取，并执行相应的处理。通过使用 Annotation程序员可以不改变原有逻辑的情况下，在源文件中嵌入一补充信息。代码分析工具、开发工具和部署工具可以通过这些这写补充信息进行验证或者部署。

- Annotation 可以像修饰符一样被使用，可用于修饰包、类构造器、方法、成员变量、参数、局部变量的声明，这些信息被保存在 Annotation 的 "name = value " 中。

- 在 JavaSE 中，注解的使用目的比较简单，例如，标记过时的功能，忽略警告等。在 JavaEE / Android 中注解占据了重要的角色，例如用来配置应用程序的任何切面，代替 JavaEE 旧版本中年的繁冗代码和 XML 配置。

- 未来的开发模式都是基于注解的，JPA 是基于注解的，Spring2:5 以上都是基于注解的，Hibernate3.X 以后也是基于注解的，现在 Struts2 有一部分也是基于注解的了，注解是一种趋势。一定程度上可以说：框架 = 注解 + 反射 + 设计模式。

**常见的 Anotation 实例**

- 使用 Annotation 时要在前面加 @ 符号，并把 Annotation 当作一个修饰符使用。用于修饰它支持的程序元素。

- 实例一：生成文档相关的注解

  > - @author 表明开发该模块的作者，多个作者之间用逗号隔开
  >
  > - @verison 表明该模块的版本
  >
  > - @see 参考转向，也就是相关主题
  >
  > - @since 从那个版本开始增加的
  >
  > - @param 对方法中某参数的说明，如果没有参数就不写
  >
  > - @return 对方法返回值的说明，如果方法的返回值是 void 就不能写
  >
  > - @exception 对方法可能抛出的异常进行说明，如果方法没有 throws 显式的抛出异常就不能写其中
  >
  > - 说明：
  >
  >   @param @return 和 @exception 这三个标记都是只用于方法的
  >
  >   @param 的格式要求： @param 形参名 形参类型 形参说明
  >
  >   @return 的格式要求：@return 返回值类型 返回值说明
  >
  >   @exception 的格式要求：@exception 异常类型 异常说明
  >
  >   @param 和 @excption 可以并列多个

实例二：在*编译时*，进行格式检查(JDK 内置的三个基本注解)

> - @Override: 限定重写父类方法，该注解只能用于方法
> - @Deprecated: 用于表示修饰的元素 (类、方法) 等都已过时。通常是因为所修饰的结构危险或存在更好的选择
> - @SuppressWarnings({String[] values}): 抑制编译器警告  unused 没有使用

实例三：跟踪代码的依赖性，实现配置文件功能

> - Servelt3.0 提供了注解（annotation）,使得不再
>   需要在 web.xml 文件中进行 servelt 的部署。
> - spring 框架中关于 ”事务“ 的管理。

如何自定义注解：

参照 @SuppressWarnings 定义

> - 自定义 Annotation 类，声明要用 @interface 修饰
>
> - 自定义注解自动继承了 java.lang.annotation.Annotation 接口。
>
> - Annotation 的成员变量在 Annotation 定义中以无参数方法的形式声明。其方法名和返回值定义了该成员的类型和名称。我们称为配置参数。类型只能是八种基本数据类型、String 类型、Class 类型、Enum 类型、Annotation 类型、及以上所有类型的数组
>
> - 可以在定义 Annotation 的成员变量时指定其默认值，成员变量的初始值使用 default  修饰。
>
>   格式为：类型 名称() default 初始值。
>
> - 如果定义的注解含有配置参数，那么使用时必须指定参数值，除非它有默认值。格式：注解名称（“参数名 = 参数值”）
>
> - 没有成员定义的 Annotation 称为标记；包含成员变量的 Annotation 称为元数据 Annotation
>
>   注意：自定义 Annotation 必须配上信息处理流程 (反射) 才有意义。
>
> - 如果只有一个成员变量，建议使用 value 表示
>
> - 如果自定义注解没有成员，表明是一个标识作用。
>
> 格式为：权限修饰符 @interface 注解名称{
>
> }

**JDK4 中的元注解**

>- 元注解就是现有注解，进行解释说明的注解。（JDK 中的元 Annotation 时用来修饰其它 Annotation 定义。)
>
>- JDK 5.0 提供了 4 个标准 meta-Annotation 类型，分别是：
>
>- Retention: 指定所修饰的Annotation 的生命周期（一次只能修饰一个 Annotation 定义）。@Retention 包含一个 RetentionPolicy 类型的成员变量，使用 @Retention 时必须为该 Value 成员变量指定值：
>
>  - RetentionPolicy.SOURCE：在源文件中有效(源文件保留)，编译器直接丢弃的这种策略 
>  - RetentionPolicy.CLASS ：在 class 文件中有效（即 class 保留），当运行 Java 程序时，JVM 不会保留注释，这是默认值。
>  - RetentionPolicy.RUNTIME:在运行时有小(即运行时保留，)，当运行 java 程序时，JVM 会保留注释，程序可以通过反射获取注释。 (只有声明为 RUNTIME 生命周期的注解，才能通过反射获取）
>  - 格式：@Retention(RetentionPolicy.状态)
>
>- Target：用于指定被修饰 Annotation 能用于修饰哪些程序元素。@Target 也包含一个名为 value 的成员变量
>
>  | 取值(Element Type) |                    | 取值(Element Type) |                                               |
>  | ------------------ | ------------------ | ------------------ | --------------------------------------------- |
>  | CONSTRUCOTR        | 用于描述构造器     | PACKAGE            | 用于 描述包                                   |
>  | FIELD              | 用于描述域(即属性) | PARAMETER          | 用于描述参数                                  |
>  | LOCAL_VARIABLE     | 用于描述局部变量   | TYPE               | 用于描述类、接口（包括注解类型）或 enum 声明; |
>  | METHOD             | 用于描述方法       |                    |                                               |
>
>- Documented: 用于指定被该元 Annotation 修饰的 Annotation 类将被 JavaDoc 工具提取成文档。默认情况 JavaDoc 是不包括注解的
>
>  - 定义为 Doucmented 的注解必须设置 Retention 的值为 RUNTIME
>
>- Inherited：被它修饰的 Annotation 将具有继承性。如果某个类使用了 @inherited 修饰的 Annotation,则子类自动具有该注解。
>
>  - 比如：如果把标有 @Inherited 注解的自定义的注解标注在类级别上，子类则可以继承父类级别的注解。
>  - 在实际应用中应用较少
>
>  通过反射来获取注解信息。
>
>自定义注解，通常会指明两个元注解：RetentionPolicy、Target

**JDK8 中注解的新特性：可重复注解、类型注解**

**可重复注解**

> JDK 8.0 之前的写法：
>
> @MyAnnotation({@MyAnnotation value = "hi",@MyAnnotation value = "Hello"}); //这里的 @MyAnnotation 为自定义注解类
>
> JDK 8.0 之后：
>
> @Repeatable(MyAnnotation.class);
>
> 1. 在 MyAnnotation 上声明 Repeatable成员值为 MyAnnotation.class  //这里的 @MyAnnotation 为自定义注解类
> 2. MyAnnotation 的 target 、Retention、Inherited ，和 MyAnnotations 相同
>
> 举例：
>
> ```java
> @Inherited
> @Repeatable(MyAnnotations.class)
> @Retention(RetentionPolicy.Runtime)
> @Target({TYPE,FIELD,METHOD,PARAMETER,CONSTRUCTOR,LOCAL_VARIABLE})
> public @interface MyAnnotation(){
>     String value() default "Hello";
> }
> 
> @Retention(RetentionPolicy.Runtime)
> @Target({TYPE,FIELD,METHOD,PARAMETER,CONSTRUCTOR,LOCAL_VARIABLE})
> ```

**类型注解**

> - JDK 1.8 之后，关于元注解 @Target 的参数类型 ElementType 的枚举值多了两个： TYPE_PARAMETER,TYPE_USE
> - 在 Java 8 之前，注解只能是在声明的地方使用，从 Java 开始可以应用在任何地方。
> - ElementType.TYPE_PARAMETER 表示该注解能写在类型变量声明的语句中(泛型声明中)。
> - ElementType.TYPE_USE 表示该注解能在 使用类型的任何语句。

### 第四章 Java 集合

##### 1. Java 集合框架概述

> - 面向对象语言对事物的体现都是以对象的形式，为了方便对多个对象进行操作，就需要对对象进行存储，一方面，使用 Array 存储对象具有一些弊端；而 Java 集合就像一种容器，可以动态把多个对象(引用)放入容器。
>   - 数组在内存存储方面的特点
>     - 数组初始化以后，长度就确定了
>     - 数组一旦声明(定义)之后，元素的类型也就确定了
>   - 数组在存储数据方面的弊端
>     - 数组初始化之后，长度就不可改变，不便于扩展
>     - 数组中提供的属性和方法有限，不便于进行添加、删除、插入等操作，且效率不高同时无法直接获取存储元素的个数。（获取数组中实际元素的个数的需求，数组没有属性和方法可以调用。
>       ）
>     - 数组存储的数据是有序，可以重复的。对于无序，不可重复的需求不能满足。
> - Java 集合类可以用于存储数量不等的对象，还可以保存具有映射关系的关联数组。

1. 集合和数组都是对多个数据进行存储操作的结构，简称 Java 容器

   说明：此时的存储，主要指的是内存层面的存储，不涉及到持久化存储 (存储到硬盘中)。

2. 集合框架的应用场景：

   客户端向服务器请求数据，服务器通过数据库进行查询，数据库中查询到的数据（对象或对象构成的 List）返回到服务器，服务器然后将对象转换为 JSON 对象或JSON 数组，然后通过网络传输给客户端，客户端将 JSON 对象或数组转换 Java 对象或 Java 对象构成的 List

   ![集合框架的应用场景](C:/doc/Markdown文档/Java/集合框架的应用场景-1566019365942.png)

3. Java 集合可以分为 Collection 和 Map两种体系 

   **集合框架**

   /-----Collection: 单列数据，保存一组对象

   ​	/----List 接口：元素有序，可重复的集合 

   ​			/-----ArrayList、Linkedlist、Vector

   ​	/---- Set 接口:元素无序、不可重复的集合

   ​			/----HashSet、LinkedHashSet、TreeSet

   /-----Map 接口：双列数据，保存具有映射关系的 "Key-Value对" 集合。

    			/----HashMap、LinkedHashMap、TreeMap、Hashtable、Propertiess

虚线为实现，实线为继承：

![image-20200106233645536](C:/Users/XJ/Documents/Markdown文档/image-20200106233645536.png)

##### 2. Collection 接口方法

JDK 8 以前的 API：

| 方法名                        | 描述                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| add(object  e)                | 将元素 e 添加到集合中                                        |
| size()                        | 获取元素的个数                                               |
| addAll(Collection  coll);     | 将 coll 集合中的数据添加到当前集合中                         |
| isEmpty();                    | 判断当前集合是否为空(当前集合中是否有元素)                   |
| clear()                       | 清空当前集合的元素                                           |
| contains(Object obj)          | 判断当前集合中是否包含 obj<br />**在判断时，会调用 obj 所在类的 equals 方法** |
| containsAll(Collections coll) | 判断 coll 中的所有元素是否都存在于当前集合中。               |
| remove(Object obj);           | 删除当前集合中的 obj  元素                                   |
| removeAll(Collection coll1)   | 从当前集合中移除 coll 中的所有元素                           |
| retainAll(Collection coll)    | 获取当前集合和 coll 集合的交集。将结果保留在当前集合         |
| equals(Object obj)            | 要想返回 true，需当前集合和形参集合的所有元素都相同          |
| hashCode()                    | 获取当前集合的哈希值                                         |
| toArray()                     | 把集合转换为数组。                                           |
| Arrays.asList()               | 把数组转换为集合。                                           |
| iterator()                    | 返回 Iterator 接口的实例，用于遍历集合的元素。放在 IteratorTest.java 中测试。 |
|                               |                                                              |

向 collection 接口的实现类的对象中添加数据 obj 时，要求 obj 所在类重写 equals 方法

```java
//数组 ---> 集合
List arr1 = Arrays.asList(new int[]{123,456});
System.out.println(arr1.size);//1
List arr2 = Arrays.asList(new Integer{123,456});
System.out.println(arr2.size());//2
//集合会把基本数据的数组认为是一个对象。
```

##### 3. Iterator 迭代器接口

> - Iterator 对象称为迭代器(设计模式的一种)，
> - GOF (23 种经典设计模式的提出者 )：提供一种方法访问一个容器 (container) 对象中的各个元素，而又不需要要暴露该对象的内部细节。迭代器模式，就是为容器而生。
> - Collection 接口继承了 java.lang.Iterablbe 接口，该接口种有一个 iterator 方法，那么所有实现了 Collection 接口的集合类都有一个 iterator 方法，用以返回一个实现了 iterator 接口的对象。
>
> **Iterator**
>
> - Itreator 仅用于遍历集合，Iterator 本身并提供承装对象的能力
> - *集合对象每次调用 iterator() 方法都会得到一个全新的迭代对象，默认游标都在集合的第一个元素之前。*
> - iterator 可以删除集合中的元素，但是是遍历过程中迭代器对象的 remove 方法，不是集合中的 remove 方法。
> - 如果还为调用 next() 或在上一次调用 next() 发方法之后调用了 remove() 方法，再调用 remove 会报 illegalStateException

集合元素的遍历，使用 Iterator 接口。

```java
Collection coll = new ArrayList{123，123，2312，234，34，34，223，“123123d”}):
Iterator iterator = coll.iterator();
//方式一：
System.out.println(iterator.next());//123
System.out.println(iterator.next());//123
System.out.println(iterator.next());//2312
System.out.println(iterator.next());//234
……
System.out.println(iterato.next());//"123123d"
//noSuchElementException
System.out.println(iterator.next());
//方式二：
for(int i = 0;i < coll.size();i++){
	System.out.println(iterator.next());
}
//方式三：
while(iterator.hasNext()){//判断集合中是否还有下一个元素
    System.out.println(itreator);//1. 指针下移 2. 将下移以后集合位置上的元素返回。
}
```

 iterator 遍历集合的两种错误的写法

```java
//错误方式一：
Iterator iterator = coll.iterator();
while(iterator.next() != null){
    System.out.println(iterator);//会出现情况 1：集合中的元素会隔一个输出一个
}
//错误方式二：
while(coll.iterator().hasNext()){
    System.out.println(coll.iterator().next());//每一次调用 iterator() 都会产生一个新的对象，因此会一直输出第一个元素
}
```

**Iterator 迭代器 remove 的使用**

```java
Iterator iterator = coll.iterator();
while(iterator.hasNext()){//判断集合中是否有下一个元素
    Object obj = iterator.next();//1. 指针下移 2. 返回当前指针指向的元素
    if("Tom".equals(obj)){//移除集合中的 "Tom 元素"
        iterator.remove();
    }
}

//对 coll 集合中的元素进行遍历输出
Iterator iterator1 = coll.iterator():
while(iterator1.hasNext()){
    //为什么需要新建一个 Iterator 对象而不直接使用上面的哪一个？
    //因为上面的那个 Iterator 对象指针已经已经移动到了集合 coll 的元素。 
    Object obj = iterator1.next();
    System.out.println(obj):
}
```

**JDK 5.0 新增了 foreach 遍历集合和元素**

```java
//格式：for(集合类型 局部变量: 集合对象){
//  
//}
//将集合内元素顺序取出，然后赋值给局部变量。但内部仍然调用了迭代器 
for(Object obj:coll){
    System.out.println(obj);
}

//常见错误
String[] strArr = new String[]{"MM","MM","MM"};
//第一种使用 for 循环
for(int i = 0;i < arr.length;i++){
    strArr[i] = "GG";
}
System.out.println(Arrays.toString(strArr)); //[“GG”,“GG”,“GG ”]
解释:我们使用 for 循环是将这个数组中元素进行改变
//第二种使用 foreach
for(String str:strArr){
    str = "GG";//我们这里相当于把 strArr 中的元素取出然后赋值给 str。然后我们是对 str 的值进行了改变，而字符串具有不可变性因此还是 “MM”
}
System.out.println(strArr);//["MM","MM","MM"]
//解释：我们使用这种方式是将字符串进行改变，而字符串据有不可变性。
```

##### 4. Collection 子接口 1 :  List 

存储数据特点：有序，可重复

> - 鉴于 Java 中用数组来存储数据具有局限性。我们通常使用 List 来代替数组
> - List 集合类中元素有序、可重复，集合中的每个元素都有其对应的顺序索引
> - List 容器中的元素都对应一个整数型的序号记载其在容器中的位置，可以根据序号存取容器的元素
> - JDK API 中 List 接口的实现类常用的有：ArrayList、LinkedList 和 Vector

**ArrayList、LinkedList 和 Vector 三者的异同：**

相同点：三个类都实现了 List 接口，存储数据的特点相同：存储有序，可重复的数据。

> - ArrayList 作为 List 接口的主要实现类；线程不安全，效率高；底层使用 Object[] elementData 存储。
> - LinkedList：对于频繁的插入，删除操作，使用此类效率比 ArrayList 高；底层使用双向链表存储。
> - Vector: 作为 List 的古老实现类，线程安全，效率低，底层使用 object[] elementData 存储。

**ArrayList 的源码分析**  JDK 7.0 情况下

```java
ArrayList list = new ArrayList(); //底层帮我们创建了长度为 10 的 Object[]  elementData
list.add(123);//elementData[0] = 123;
……
list.add(11);//如果此次的添加导致底层elementData 数组容量不够，则扩容，默认情况下，扩容为原来容量的 1.5 倍，同时将原有数组中的内容复制到新的数组中。
```

结论：建议在开发中使用带参的构造器。ArrayList list = new Arraylist(int capacity);

**JDK 8 情况下**

```java
ArrayList list = new ArrayList;//底层 Object[] elementData 初始化为 {}，并没有创建
list.add(123);//第一此调用 add() 时，底层才创建了长度为 10 的数组，并将数据添加到 elementData 中。
//后续的添加和扩容操作和 JDK 7.0 无异
```

总结：JDK 7.0 中的 ArrayList 的对象的创建类似于单例模式的饿汉模式，而 JDK 8.0 中的 ArrayList 的对象的创建 类似于单例模式的懒汉式，延迟的数组的创建(节省内存)。

**LinkedList 的源码分析**

```java
LinkedList list = new LinkedList();//内部声明了 Node 类型的 first 和 last 属性，默认值为 null
list.add(123);//将 123 封装进 Node 中，创建了 Node 对象。
其中,Node 定义为：体现了 LinkedList 的双向链表的说法：
private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

**Vector 的源码分析**

```java
JDK7 和 JDK8 中通过 Vector() 构造器来创建对象时，底层都创建了长度为 10 的数组
扩容方面，默认扩容为原来的数组的长度的 2 倍。
```

**List 接口中常用方法的测试**

> List 除了从 Collection 集合继承的方法，List 集合添加了一些根据索引来操作集合元素的方法。
>
> | 名称                                        | 描述                                                   |
> | ------------------------------------------- | ------------------------------------------------------ |
> | void add(int index,Object ele);             | 在指定 index 位置插入 ele 元素。                       |
> | boolean addAll(int index,Collection eles) ; | 从指定 index 位置开始，将 中的所有元素添加进来。、     |
> | Object get(int index);                      | 获取指定 index 位置的元素                              |
> | int inexOf(Object obj);                     | 返回 obj 在集合中首次出现的位置。如果不存在则返回 -1   |
> | int lastInexOf(Object obj);                 | 返回 obj 在当前集合中末次出现的位置                    |
> | Object remove(int index);                   | 移除指定 index 位置的元素，并返回此元素                |
> | Object set(int index,Object ele)            | 设置指定 index 位置的元素为 ele                        |
> | List subList(int fromIndex,int toIndex)     | 返回从 fromInde 到 toIndex (左闭右开全金) 位置的子集合 |

常用方法：

```java
增：add(Object obj)
删:remove(int index)/remove(Object obj)
改：set(int index,Object ele)
查：get(int index)
插：add(int index,Object ele)
长度：size()
遍历：
	1. Iterator 迭代器方式
	2. 增强 for 循环方式
	3. 普通的循环
```

##### 5. Collection 子接口2 ：set

> 存储无序、不可重复的数据。
>
> > **set 接口**
> >
> > /------HashSet:作为 Set 接口的主要实现类。线程不安全的，可以存储 null 值。
> >
> > ​		/-----LinkedHashSet:  作为 HashSet 的子类，遍历其内部数据时，可以按照添加的顺序遍历。
> >
> > /------TreeSet：可以按照添加对象的指定属性进行排序。

**HashSet:**

>一、set 集合的无序性和不可重复性
>
>**无序性：** 不等于随机性。存储的数据在数组中并非是按照数组索引的顺序添加，而是由数据的哈希值确定的‘。
>
>**不可重复性：** 保证添加的元素按照 equals() 判断时，不能返回 true,即相同的元素只能添加一个。
>
>1. set 接口中没有额外定义新的方法，使用的都是 Collection 中声明的方法。
>
>2. 要求：向 Set 中添加的数据，其所在类的一定要重写 hashCode() 和 equals() 方法。
>
>  重写的 hashCode() 和 equals() 要尽量保持一致性：相等的对象必须具有一致的散列码。
>
>  重写两个方法的小技巧：对象中用作 equals() 方法比较的 Field,都应该来计算 hashCode
>
>二、**HashSet 中元素的添加过程**，以 HashSet 为例说明
>
>我们向 HashSet 中添加元素 a,首先调用 a 所在类的 hashCode() 方法，计算元素 a 的哈希值，此哈希值接着通过某种算法计算 (散列函数) 出在 HashSet 中底层数组中的存放位置 (即为索引位置)。判断数组此位置上是否有元素。
>
>- 如果此位置上没有其它元素，则 a 添加成功。-----> 情况 1 
>
>- 如果此位置上有其它元素 b (或是以链表形式存在的多个元素)，则比较元素 a 和元素 b 的哈希值。
>- 如果 hash 值相同，则元素 a 添加成功 -----> 情况 2
>- 如果 hash 值不同，进而需要调用元素 a 所在类的 equals() 方法。
> - 如果返回 true,元素 a 添加失败
> - 如果返回 false，元素 a 添加成功。----->情况 3
>
>对于添加成功的情况 2 和情况 3 而言: 元素 a 与存放在指定位置上的数据以链表的形式存储。
>
>jdk7 : 元素 a 在数组中，指向原来的元素
>
>jdk8 : 原来的元素在数组中，指向元素 a
>
>总结：七上八下
>
>HashSet 底层：数组 + 链表的结构。

关于 hashCode() 和 equals() 的重写：

> 以 Eclipase/IDEA 为例，在自定义类中可以调用工具重写 hashCode() 和 equals() 方法。
>
> 问：为什么用 Eclipse/IDEA 重写 hashCode 方法，有 31 这个数字。
>
> - 选择系数要选择尽量大的系数，因为如果计算出来的 hash 值越大 “冲突” 就越小。
> - 并且 31 只占 5 bits,相乘造成数据溢出概率较小
> - 31 可以有 i*31==(i<<5)-1,想在很多虚拟机都在做相关优化 (提高算法效率)
> - 31 是一个素数，素数的作用就是如果我们用一个数来乘以这个素数，那么最终出来的结果只能被素数本身和被乘数还有 1 来整除(减少冲突)。 
>
> **这里的减少冲突，只是可以在一定程度上减少冲突，但并不代表没有冲突。**
>
> HashSet 的底层是 HashMap

**LinkedHashSet**

>LinkedHashSet 作为 HashSet 的子类，在添加数据的同时，每个数据还维护两个个引用，记录此数据前一个数据和后一个一个数据。
>
>好处：对于频繁的遍历操作，LinkedHashSet 的效率要高于 HashSet

**TreeSet**:

> 可以按照对象的指定属性排序。
>
> **1.** 向 TreeSet 中添加数据，要求的相同类的对象
>
> 自然排序（实现 Comparable 接口）：比较两个对象是否相同的标准是 comparaTo() 返回 0，不再是 equals()
>
> ```java
> //按照姓名从小到大进行排序
> public User implements Comparable{
> private String name;
> ……
> public ing compareTo(Object o){
>   if(o instanceof User){
>       User user = (User)o;
>       return this.name.compareTo(user.name)
>   }else{
>       throw new RuntimeException("输入的类型不匹配");
>    }
> }
> }
> ```
>
> 定制排序 (实现 Comparator)：
>
> 比较两个对象是否相同的标准是 compara() 返回 0，不再是 equals()
>
> ```java
> public void test1(){
> Comparator com = new Comparator{
> //按照指定的年龄从小到大排列
>   @Override
>   public int compare(Object o1,Object o2){
>       if(o1 instanceof User && o2 instanceof User){
>           User u1  = (User)o1; 
>           User u2  =  (User)o2;
> 	    return Integer.compare(u1.getAge(),u2.getAge());
>       }
> }
> }
> TreeSet set = new TreeSet(com);
> }else{
> throw new RuntimeException("输入的类型不匹配");
> //还可以写宽松一点的写法：return 0 因为 TreeSet 不可以存放相同的数据所以如果返回 0 则这个元素不会倍存放到 TreeSet 中
> }
> ```

**set 接口实现类的对比**



##### 6. Map 接口

Map 接口以及多个实现类的对比：

![map](C:/doc/Markdown文档/Java/map.png)

其中：`Hashtable,Properties,HashMap,LinkedHashMap,TreeMap` 为 Map 接口的实现类，其中 TreeMap 实现了 SortedMap 接口，StoredMap 接口又实现了 Map 接口

```
/----Map：双列数据，存储 key-value 对的数据。 ——————— 类似于高中的函数：y = f(x)
		/----HashMap:作为 Map 的主要实现类;线程不安全的，效率高;可以存储值为 null 的 key-value
			/----LinkedHashMap:保证在遍历 map 元素时，可以按照添加的元素顺序进行遍历。
			原因：在 HashMap 的底层基础结构上，添加了一对指针，指向前一个和后一个元素；对于频繁的遍历操作，此类的执行效率要高于 HashMap
		/----TreeMap:保证可以按照添加的 key-value 对进行排序，实现遍历排序
		/----Hashtable:作为 Map 的古老实现类;线程安全的，效率低。不可以存储值为 null 的 key-value。此时考虑 key 的自然排序或定制排序。
		底层使用红黑树(TreeSet)
			/----Properties:常用来处理配置文件。key 和 value 都是 String 类型。

HashMap 的底层：数组 + 链表(JDK7 及以前)
HashMap 的底层：数组 + 链表 + 红黑树(JDK 8)
```

面试题：

1. HashMap 的底层实现原理？

**以 JDK7 为例说明：**

```java
HashMap map = new HashMap();//底层创建了长度是 16 的一维数组 Entry[] table
…
可能已经执行过多次 put
…
map.put(key1,value1);//首先调用 key1 所在类的 HashCode 计算 key1 的哈希值。此哈希值经过某种算法以后得到在 Entry 数组中的存放位置。
//如果此位置上的数据为空，此时 key1-value1 添加成功。-------情况一
//如果此位置上的数据不为空(意味着此位置上存在一个或多个数据（以链表形式行在）)，比较 key1 和已存在的一个或多个数据的哈希值：
	//如果 key1 的哈希值与已经存在的数据的哈希值都不相同，此时 key1-value1 添加成功。-------- 情况二
	//如果 key1 的哈希值与已经存在的某一个数据(key2-value2)的哈希值相同，继续比较:调用 key1 所在类的 equals() 方法。
	//如果 equals 返回 false: 此时 key1-value1 添加成功 -------- 情况三
	//如果 equals 返回 true: 使用 value1 替换 value2
	
//补充：关于情况 2 和情况 3：此时 key1-value1 和原来的数据以链表的方式存储。

//在不断添加的过程中会涉及到扩容的问题：默认的扩容方式，扩容为原来的 2 倍（当超出临界值且要存放的位置非空时），并将原来的数据复制过来。
```

**在 JDK8 中 HashMap 在底层中的实现原理：**

JDK 8 相较于 JDK7  在底层实现方面的不同：

> - new HashMap(): 底层没有创建一个长度为 16 的数组
>
> - JDK 8 底层的数据是：Node[] 而非 Entry[]
>
> - 首次调用 put 方法时，底层创建当都为 16 的数组
>
> - JDK 底层的结构只有：数组 + 链表。JDK8 中底层结构：数组 + 链表 +  红黑树
>
>   当数组的某一个索引位置上的元素以链表形式存在数据个数 > 8 且当前数组的长度 > 64 时，此时此索引位置上的所有元素改用红黑树存储。 

HashMap 在 JDK7 中的源码分析：

HashMap 中的重要常量：

![HashMapImportFinalCapacity](C:/Users/XJ/Documents/Markdown文档/HashMapImportFinalCapacity.png)

```java
TREEIFY_THRESHOLD:Bucket 中链表长度大于该默认值时，会转换为红黑树：8
MIN_TREEIFY_CAPACITY:桶中的 Node 被树化时的最小容量：64
```

JDK7 中

```java
public HashMap(){
    return HashMap();
}

```

HashMap 在 JDK8 中的源码分析：

```java
static final float DEFAULT_LOAD_FACTOR = 0.75f;
public HashMap() {
        this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted 默认加载因子设置为 0.75
}
```

LinkedHashMap 的底层实现(了解)：

```java

```

2. Hashtable 和 HashMap 的异同？

3. CurrentHashMap 与 Hashtable 的异同？

**key-value 结构的理解**

Map 中存储的 key-value 的特点？

key 中存储的数据: 不可重复，无序 ，使用 Set 存放所有的 key ————— key 所在的类要重写 equals() 和 hashCode() // 以 HashMap 为例。

value 中存储的数据：可以重复，无序 (由于没有对应的这种结构，因此泛称为用 Collection 存储)。———— value 所在的类要重写 equals()

疑问：为什么要重写 equals() ?

key 和 value 存入 Map 中虽然是两个数据，但是会被看作为是一个对象（entry），这两个数据会被看作为是 entry 的属性，也就说一个键值对： key-value 构成了一个 entry 对象。

entry 的特点：无序、不可重复，使用 Set 存储所有的 entry

**LinkedHashMap 的底层实现原理(了解)：**

```java
LinkedHashMap 继承于 HashMap: 
源码中：
 \\static class Entry<K,V> extends HashMap.Node<K,V>{
 Entry<K,V> before,after;//能够记录添加元素的先后顺序
 Entry(int hash,k key,V value,)
 }
```

Map 中常用方法：

添加、删除、修改操作：

| Object put(Object key,Object vlaue); | 将指定 key-value 添加到（或修改） 当前 map 中 |
| ------------------------------------ | --------------------------------------------- |
| void putAll(Map m)                   | 将 m 中所有的 key-vlaue 对存放到当前 map 中   |
| Object remove(Object key)            | 移除指定 key 的 key-value 对，并返回 value    |
| void clear()                         | 清空当前 map 中的所有数据                     |

元素的查询操作：

| Object get(Object key)              | 获取指定 key 对应的 value         |
| ----------------------------------- | --------------------------------- |
| boolean containsKey(Object key)     | 是否包含指定的 key                |
| boolean containsValue(Object value) | 是否包含指定的 value              |
| int size()                          | 返回 map 中 key-value 对的个数    |
| boolean isEmpty()                   | 判断当前 map 是否为空             |
| boolean equals(Object obj)          | 判断当前 map 和参数  obj 是否相等 |

原视图操作的方法

| Set keySet()        | 返回所有 key 构成的 Set 集合          |
| ------------------- | ------------------------------------- |
| Collection values() | 返回所有 value 构成的 collection 集合 |
| Set entrySet()      | 返回所有 key-value 对构成的 Set 集合  |

```java
//遍历 map 集合中所有的 key 集:keySet
Set set = map.key;
//使用迭代器
Iterator iterator = set.iterator();
while(iterator.hasNext()){
	System.out.println(iterator.next());
}
// 遍历 map 集合中所有的 value 集：values()
Collection values = map.values();
//使用增强 for 循环
for(Object obj:value){
	System.out.println(obj);
}
//遍历所有的 key-value
//方式一：
Set entrySet = map.entrySet();
Iterator itrerator1 = entrySet.iterator();
while(iterator1.hasNext()){
    Object obj = iterator.next();
    Map.Entry entry = (Map.Entry)obj;
    System.out.println(entry.getKey(),entrygetValue())
}
方式二：
Set keySet = map.keySet();
Iterator iterator2 = keySet.iterator();
while(iterator2.hasNext()){
    Object key = iterator2.next();
    Object value = map.get(key);
	System.out.println(key+"========"+value)    
}
总结：
添加：put(Object key,Object value)
删除：remove(Object key)
修改：put(Object key,Object value)
查询：get(Object key)
长度：size()
遍历:keySet()/values()/entrySet()
```



```java
//遍历 map 集合中所有的 key 集:keySet
Set set = map.key;
//使用迭代器
Iterator iterator = set.iterator();
while(iterator.hasNext()){
	System.out.println(iterator.next());
}
// 遍历 map 集合中所有的 value 集：values()
Collection values = map.values();
//使用增强 for 循环
for(Object obj:value){
	System.out.println(obj);
}
//遍历所有的 key-value
//方式一：
Set entrySet = map.entrySet();
Iterator itrerator1 = entrySet.iterator();
while(iterator1.hasNext()){
    Object obj = iterator.next();
    Map.Entry entry = (Map.Entry)obj;
    System.out.println(entry.getKey(),entrygetValue())
}
方式二：
Set keySet = map.keySet();
Iterator iterator2 = keySet.iterator();
while(iterator2.hasNext()){
    Object key = iterator2.next();
    Object value = map.get(key);
	System.out.println(key+"========"+value)    
}
总结：
添加：put(Object key,Object value)
删除：remove(Object key)
修改：put(Object key,Object value)
查询：get(Object key)
长度：size()
遍历:keySet()/values()/entrySet()
```

**TreeMap 两种添加方式的使用：**

```java
//向 TreeMap 中添加 key-value,要求 key 必须是同一个类创建的对象。
//因为要按照 key 进行排序：自然排序、定制排序
public void test1(){
    TreeMap map = new TreeMap();
    User u1 = new User("Tom",23);
    User u1 = new User("Jack",21);
    User u1 = new User("Jerry",18);
    User u1 = new User("Rose",17);
    map.put(u1,98);
    map.put(u2.89);
	    
}

```

Properties 处理属性文件：

> - Properties 类是 Hashtable 的子类，该对象用于属性文件
>
> - 由于属性文件里的 key、value 都是字符串类型，所以 Properties 里的 key 和 value 都是字符串类型
> - 存取数据时，建议使用 setProperty(String key,String value) 方法和 getProperty(String key) 方法
>
> ```java
> FileInputStream fis = null;
> try{
>     Properties pros = new Properties();
>     fis = new FileInputStream("要读取的Property 文件");
>     pros.load(fis);//加载流对应的文件
>     String name = pros.getProperty(“name”);
>     String password = pros.getProperty(“password”);
>     System.out.println("name" + name + "password" + password);
> }catch(IOEexception e){
>     e.printStackTrace();
> }finally{
>     if(fis != null){
>         try{
>             fis.close();
>         }catch{
> 
>         }
>     }
> }
> ```
>
> 

##### 7. Colletions 工具类

> - Collections 是一个操作 Set、List、Map 等集合的工具类
>
> - Collections 中提供了一系列的静态方法对集合元素进行排序、查询、修改等操作，还提供对集合对象设置不可变、集合对象实现同步控制等方法
>
> - 排序操作：
>
>   - reverse(List): 反转 List 中元素的顺序
>   - shuffle(List): 对 List 集合元素进行随机排序
>   - sort(List): 根据元素的自然顺序对指定 List 集合按升序排序
>   - sort(List,Comparator): 根据 Comparator 产生的顺序对 List 集合元素进行排序
>   - swap(List list,int i,int j): 将指定 list 集合中的 i 处元素和 j 处元素进行交换
>
>   查找
>
>   Object max(Collection): 根据元素的自然排序，返回给定集合中最大的元素。
>
>   Object max(Collection,Comparator): 根据 comparator 指定的顺序，返回给定集合中的最大元素
>
>   Object min(Collection):
>
>   Object min(Collection,Comparator):
>
>   int frequency(Collection,Object ): 返回指定集合中指定元素出现的次数。
>
>   void copy(List dest,List src): 将 src 中的内容复制到 dest 中
>
>   boolean replaceAll(List list,Object oldVal,Object newVal)：使用新值替换 List 中对应的旧值
>
>   ```java
>   List list = new ArrayList();
>   list.add(123);
>   list.add(43);
>   list.add(765);
>   list.add(-97);
>   list.add(0);
>   
>   /*报异常IndexOutOfBoundsException("source does not fit in dest")
>   List dest = new ArrayList();
>   Collections.copy(dest,list);
>   */
>   
>   //正确的写法
>   List dest = Arrays.asList(new Object[list.size()]);
>   Collections.copu(dest.list);
>   System.out.println(dest);
>   
>   ```
>
> 
>
>   **同步控制**：
>
>   Collections 类中提供了多个 synchronizedXXX() 方法，该方法将使指定集合包装成线程同步的集合，从而可以解决多线程并发访问集合时的线程安全问题。
>
>   synchronizedCollection(Collection<T> c )
>
>   synchronizedCollection(List<T> list)
>
>   synchronizedMap(Map<K,V> m)
>
>   synchronizedSet(Set<T> s)
>
>   synchronizedSortedMap(SortedMap<K,V> n)
>
>   synchronizedSortedSet(SortedSetM<T> s)

Collections 和 Collection 的区别？

Collection 是实现集合的接口。Colloections 是操作 Collection 的工具类还可以用来操作 Map。

Java 版数结构简述：

### 第五章 泛型与 File

##### 1. 为什么要使用泛型

我们在声明集合容器类时，根本不确定这个容器存放什么类型的对象。在 JDK 1.5 之间只能把元素类型的设定为 Object，JDK 1.5 之后使用泛型来解决。在这个时候除了集合容器类中存储元素的类型不确定，这个元素的存储，管理等其它部分都是确定的*。此时把元素的类型设计为一个参数，这个类型参数叫做泛型*。例如：Collection<E>,List<E>，ArrayList<E> 这个 E 就是类型参数，即泛型。

注：因此泛型只能是应用数据类型，而不能是基本数据类型。

**什么是泛型？**

- 允许在定义类、接口时，通过一个**标识**表示类中某个属性的类型或者是某个方法的返回值及参数类型。这个类型参数在使用时确定(传入实际的类型参数，也称为类型实参).

- 从 JDK1.5 以后，Java 引入了“参数化类型”的概念，允许我们在创建集合时指定集合的参数类型。例如:`List<String>` ,这表明在该 List 中只能保存 String 类型的对象。

- JDK1.5 改写了集合框架中全部的接口和类，为这些类和接口增加了泛型支持，从而可以在声明集合变量、创建集合对象时传入类型实参。

为什么要有泛型呢，直接用 Object 不也可以存储数据吗？

- 解决元素存储的安全性问题
- 解决获取元素时，需要强制类型转换的问题 

```java
ArrayList list = new ArrayList();
//需求存放学生成绩
list.add(67);
list.add(34);
list.add(98);
//问题一：类型不安全，list 中可以存储任何类型的元素。
list.add("Jhon");
for(Object score : list){
    //问题二：遍历，强转时，容易出现类型转换异常，ClassCastException
    int stuScore = (Integer)score;
    System.out.println(stuScore);
}
```

##### 2. 在集合中如何使用泛型

```java
//使用泛型的情况
ArrayLsit<Integer> list  = new ArrayLis<Integer>t();
//在使用泛型时，这个泛型不能是基本数据类型
list.add(67);
list.add(34);
list.add(98);
//问题一：类型不安全，list 中可以存储任何类型的元素限制。
list.add("Jhon");//会报错
//在编译时，会进行类型检查，保证数据的安全
//方式一 遍历
for(Integer score : list){
    int  stuScore = score;
      //避免强转操作
    System.out.println(stuScore);
}
//方式二 遍历
Iterator<Integer> iterator = list.iterator();
while(iterator.hasNext()){
    int stuScore = Iterator.next();
    System.out.println(stuScore);
}
```

注：在上面的代码中 ` Iterator<Integer> iterator = list.iterator();` ,Iterator  是如何知道泛型是 Integer 的？

```java
Iterator<Integer> iterator = list.iterator();
//我们发现 iterator() 方法在 Arraylist 中源码为：
public Iterator<E> iterator() {
        return new Itr();
 }//且 ArrayList 中也定义了泛型：因此返回的是同一种类型，而前面的在使用 ArrayList 在创建对象时，就已经确定了对象为 Integer 类型。
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

泛型嵌套

```java
 Map<String, Integer> map = new HashMap<String,Integer>();
        map.put("Jhon",123);
        map.put("Aosid",34);
        Set<Map.Entry<String, Integer>> entries = map.entrySet();
        Iterator<Map.Entry<String, Integer>> iterator1 = entries.iterator();
        while(iterator1.hasNext()){
            System.out.println(iterator1.next());
        }
```

`Set<Map.Entry<String,Integer>> entry = map.entrySet();//Set使用了泛型，传入的数据 map 中的内部方法 entrySet() 方法也使用了 entrySet 集合`

在集合中使用泛型总结：

1.  集合接口或集合类在 JDK5.0 时都修改为带泛型的结构。
2.  在实例化集合类时，可以指明具体的泛型类型。
3.  指明完以后，在集合类或接口中，内部结构使用类的泛型的位置，都指定为实例化时的泛型类型。
4.  注意：***泛型的类型必须是类，不能是基本数据类型，需要用到基本数据类型的位置，用包装类来替换。***
5.  如果实例化时，没有指明泛型的类型。默认类型为 java.lang.Object 类型

JDK7.0 新特性类型推断：

`HashSet<Employee> set = new HashSet<>();//也就是后面的泛型不用写`

##### 3. 自定义泛型结构:泛型类、泛型接口、泛型方法

**泛型类**

1. 泛型类的内部结构可以使用类的泛型。

2. 如果定义了泛型类，实例化时没有指明类的泛型，则认为此泛型类型为 Object

   要求：如果定义类是带泛型的，建议在实例化时要指明泛型的类型

> 在我们对类进行实例化时，使用了泛型，就会将传入该泛型的类型的类型作为参数传入该类中，然后该类就可以用该参数用来作属性、方法的返回值等。
>
> 如果子类继承了带泛型的父类时，指明了泛型的类型，则实例化子类对象时不再需要指明泛型。例如： `class SubOrder extends Order<Integer>(){}   //此时Sub 是泛型类`
>
> 子类继承了带泛型的父类，但没有指明泛型的类型，并且子类对泛型进行了保留：`class SubOrder<E> extends Order<E>(){} //此时 SubOrder<T>不是泛型类`

**自定义泛型结构：泛型类、泛型接口**

> 1. 泛型可能有多个参数，此时应将多个参数一起放在尖括号内中间用逗号隔开。例如：`<E1,E2,E3>`
>
> 2. 泛型类的构造器如下：`public GenericClass(){}`
>    错误：`public GenericCLass<E>{}`
>
> 3. 实例化后，操作原来泛型位置的结构必须与指定的泛型类型一致。
>
>    也就说实例化后，例如方法或属性处的泛型与指定的泛型一致。
>
> 4. 泛型不同的引用不能互相赋值。
>
>    例如：
>
>    ```java
>    ArrayList<Integer> list1 = null;
>    ArrayList<String> list2 = null;
>    list1 = list2;//错误在这种情况下泛型的类型就不一致了。
>    ```
>
> 5. 泛型如果不指定，则默认为Object 类型。经验：泛型要使用一路都要使用。要不用，一路都不要用。
>
> 6. 如果泛型结构是一个接口或抽象类，则不可创建泛型类的对象。
>
> 7. jdk1.7, 泛型的简化操作：ArrayList<Fruit> flist = new ArrayList<>();
>
> 8. 泛型的指定中不能使用基本数据类型，可以使用包装类替换。
>
> 9. 在类/接口中声明的泛型，在本类或本接口中即代表某种类型，可以作为非静态属性的类型，非静态方法的参数类型、返回值类型。**静态方法中不能使用类的泛型。**
>
> 10. 异常类不能是发泛型的。
>
> 11. 不能使用 new E[]。但是可以：E[] elements = (E[])new  Object(capacity);
>
>     参考 ArrayList 原码中声明：Object[] elementData = new ElementData(); 而非泛型类型数组
>
> 12. 父类有泛型，子类可以选择保留泛型，也可以选择不保留泛型。
>
>     - 子类不保留父类泛型的类型: 按需实现
>       - 没有类型擦除
>       - 具体类型
>     - 子类保留泛型的类型
>       - 全部保留
>       - 部分保留
>
> 结论：子类必须是“富二代”，子类除了指定或保留父类的类型，还可以增加自己的泛型。

**泛型方法**

> 定义：方法中出现了泛型的结构，类的泛型参数和方法的泛型参数没有任何关系。换句话说，泛型方法所属的类是不是泛型类没有关系。
>
> ```java
> public class Collection<E>{
> boolean add(E e){//这个方法就是不是泛型方法，而是一个普通的方法。
> 
> }
> }
> ```
>
> 泛型方法可以声明为静态的，因为泛型的参数是在调用的时确定的，并非实在实例化类时确定的。
>
> ```java
> public static <E> list<E> CopyFromArraytoList()
> 格式：[权限修饰符] [其它的修饰符例如final abstract static] <泛型>  [返回值类型]<泛型> 方法名
> ```

##### 4. 泛型在继承上的体现

```java
List<Object> list1;
List<Integer> list2;
list1 = list2;//此时 List1 和 LIst2 的类型不具有子父类关系
```

虽然类 A 是类 B 的父类，G<A>  和 G<B> 二者不具备子父类关系，二者是并列关系。

补充：A<G> 和 B<G> 是什么关系？A<G> 是 B<G> 的父类

证明：

```
假设：list1 = list2;
	    那么 list1 由于是 Oject 类型的那么就可以放其它的数据。
	   list1.add(123);那么可以导入非 String 类型的数据。
```

##### 5.通配符的使用

通配符: ？

类 A 是类 B 的父类：G<A> 和 G<B> 没有关系(并列)，二者的共同父类是 G<?> 

例如：

```java
List<Object> list1 = null;
List<String> list2 = null;
List<?> list   = null;

list = list1;
list = list2;
```

使用通配符后数据读取和写入要求：

1. 对于例如` list<?> `内部,就不能向其内部添加数据，除了添加 null 以外。
2. 获取（读取）：允许读取数据，读取的数据类型为 Object

用限制条件通配符：

- <?> 允许所有泛型的引用调用

- 通配符指定上限

  上限 extends: 使用是指定的类型必须是继承某个类或实现某个接口,即 <=

- 统配符指定下限

  下限 super: 只允许泛型为 Number 及 Number 子类引用的调用

- 举例：

  - `<? extends Number>`

    只允许泛型为 Number 及 Number 的子类引用调用

    `G<? extends A>` 可以作为 `G<A>` 和 `G<B>` 的父类，其中 B 是 A 的子类

  - `<? super Number>`

    只允许泛型为 Number 及 Number 的父类引用调用

    `G<? super A>` 可以作为 `G<A>` 和 `G<B>` 的父类，其中 B 是 A 的父类

  - `<? extends Comparable>`

    只允许泛型为实现 Comparable 的接口的实现类的引用调用

##### 6. 泛型使用举例

### 第六章 IO 流

##### 1. File 类的使用

1\. File 类的使用

① File 类的一个对象，文件和文件目录路径的抽象表示形式 ，与平台无关.

② File 类中涉及到关于文件或文件目录的创建、删除、重命名、获取修改时间，获取文件大小等方法，但并未涉及到写入或读取文件内容的操作。如果需要写入或读取文件内容，需要使用 IO 流来完成。

③ File 类的对象常会为作为参数传递到流构造器中，指明读取或写入的 "终点"

2\. 常用构造器

- public File(String pathname)

  以 pathname 为路径创建 File 对象，可以是相对路径或绝对路径，如果 pathname 是相对路径，则默认的当前路径在系统属性的 user.dir 中存储。

 - public File(String parent,String child)

   以 parent 为父路径，以 child 为子路径创建 File 对象

- public File(File parent,String child)

  根据一个父 File 对象和子文件路径创建 File 对象。

Tips: 想要在 Java 程序中表示一个真实存在的文件或目录，那么必须有一个 File 对象，但是 Java 程序中的一个 File 对象，可能没有真实存在的文件或目录与之对应。

相对路径和绝对路径

- 绝对路径：一个固定路径，从盘符开始

- 相对路径：相对于某个路径下，指明的路径  

> IDEA中：
> 如果大家开发使用 JUnit 中的单元测试方法测试，相对路径即为当前Module下。
> 如果大家使用main()测试，相对路径即为当前的Project下。
> Eclipse 中：
> 不管使用单元测试方法还是使用main()测试，相对路径都是当前的Project下。

3\. 路径分割符

- 路径中的每级目录之间用一个路径分隔符隔开。
- 路径分隔符和系统有关：
  - windows 和 DOS 系统默认使用 “\” 来表示
  - UNIX 和 URL 使用 “/” 来表示
  - Java 程序支持跨平台运行，因此路径分隔符要慎用。
- 为了解决这个问题，File 类提供了一个常量：
  `puglic static final String separator` 根据操作系统动态的提供分隔符。

```java
File file1 = new File("D:\\atguigu\\info.txt");// Windows 系统中
File file2 = new File("D:" + File.separator + "atguigu" + File.separator + "info.txt");//通用写法
File file3 = new File("D:/atguigu/info.txt");// Linux 系统中
```

4\. File 类常用方法 

① File 类的获取功能

- public String getAbsolutePath(): 获取绝对路径
- public String getPath(): 获取路径
- public String getName(): 获取文件名称
- public String getParent(): 获取上层文件目录路径。若无，返回 null
- public long length(): 获取文件长度,(即：字节数) 不能获取目录长度
- public long lastModified(): 获取最后一次的修改时间，毫秒值 
- public String[] list():获取指定目录下的所有文件或文件目录的名称数组
- public File[] listFiles(): 获取指定目录下的所有文件或文件目录的 File 数组

```java
        File file = new File("E:"+File.separator+"parsedocument"+File.separator+"data1.txt");
        System.out.println(file.getAbsoluteFile());
        System.out.println(file.getPath());
        System.out.println(file.getName());
        System.out.println(file.getParent());
        System.out.println(file.length());
        System.out.println(file.lastModified());
        System.out.println(Arrays.toString(file.list()));
        System.out.println(Arrays.toString(file.listFiles()));
//结果输出为
E:\parsedocument\data1.txt
data1.txt
E:\parsedocument\data1.txt
data1.txt
E:\parsedocument
107
1616942372468
null
null


        File file1 = new File("data1.txt");
        System.out.println(file1.getName());
//结果输出为：
data1.txt
    
    	File file = new File("E:"+File.separator+"parsedocument");
	    System.out.println(Arrays.toString(file.list()));
        System.out.println(Arrays.toString(file.listFiles()));
//结果
[data1.txt, parsedocument.iml, pom.xml, src, target]
[E:\parsedocument\data1.txt, E:\parsedocument\parsedocument.iml, E:\parsedocument\pom.xml, E:\parsedocument\src, E:\parsedocument\target]
```

② File 类的重命名功能：

- public boolean renameTo(File dest): 把文件重名为指定的文件路径
  - 格式：`file1.renameto(file2)`
  - 重命名 : 想要保证返回 true，file1 在文件中存在，且 file2 在文件中不存在。
  - 移动功能

原理：相当于替换了文件，即用这个路径下的文件替换另一个路径下的文件，并修改指定的文件名file2，另一个文件夹的文件不存在就是移动，如果存在，就是并将 file2 文件的内容替换为 file1 文件的内容。

③ File 类的判断功能

- public boolean isDirectory()：判断是否是文件目录
- public boolean isFile(): 判断是否是文件
- public boolean exists(): 判断是否存在
- public boolean canRead(): 判断是否可读
- public boolean canWrite(): 判断是否可写
- public boolean canHidden(): 判断是否隐藏

④ File 类创建功能

- public boolean createNewFile()：创建文件，若文件存在，则不创建，返回 else
- public boolean mkdir(): 创建文件目录。如果此文件目录存在，则不创建了。如果此文件目录的上层目录也不存在，也不创建
- public boolean mkdirs(): 创建文件目录。如果上层文件目录不存在一并创建

注意事项：如果你创建文件或者文件目录没有写盘符路径那么 默认在项目路径下 。

⑤ File 类删除功能

- public boolean delete(): 删除文件或文件夹
  删除注意事项：
  - Java 中的删除不走回收站
  - 要删除一个文件目录，请注意该 文件 目录 内 不能包含文件或者文件目录

![image-20210328231458236](C:/doc/Markdown文档/Java/Java 高级.assets/image-20210328231458236.png)

```java
        File dir1 = new File("D:"+File.separator+"IOTest"+File.separator+"dir1");
        if(!dir1.exists()){// 如果 D:/IOTest/dir1 不存在，就创建为目录,如果上层目录也不存在就不创建。
            dir1.mkdir();
        }
        //创建以 dir1 为父目录 名为 " 的 File 对象
        File dir2 = new File(dir1,"dir2");// 如果还不存在，就创建为目录
        if(!dir2.exists()){
            dir2.mkdirs();
        }
        File dir3 = new File(dir1,"dir3"+File.separator+"dir4");//创建以 dir2 为父目录 名为 "test. 的 File 对象
        if(!dir3.exists()){
            dir3.mkdirs();
        }
        File file = new File(dir2,"test.txt");// 如果还不存在，就创建为文件
        if(!file.exists()){
            try {
                file.createNewFile();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
```

##### 2. IO 流的原理及流的分类

1\. IO 概述

- I/O 是 Input/Output 的缩写， 用于处理设备之间的数据传输 。 如读写文件，网络通讯等。
- Java 程序中，对于数据的输入输出操作 以 “流 stream 的方式进行。
- java.io 包下提供了各种“流”类和接口，用以获取不同种类的数据，并通过标准的方法输入或输出数据。
- 输入 input: 读取外部数据（存储设备\键盘）到程序（内存）中。
- 输出 output: 将程序（内存）数据输出到存储设备中。

理解：流可以把它看作为二进制序列(高低电平序列)的抽象表示形式。

2\. 流的分类：

- 按操作数据单位不同分为：字节流 (8 bit)，字符流 (16 bit)
- 按数据流的流向不同分为： 输入流，输出流
- 按流的角色的不同分为： 节点流，处理流

| (抽象基类) | 字节流       | 字符流 |
| ---------- | ------------ | ------ |
| 输入流     | InputStream  | Reader |
| 输出流     | OutputStream | Writer |

① Java 的 IO 流共涉及 40 多个类，实际上非规则，都是从 4 个抽象基类派生的。

② 由这四个类派生出来的子类名称都是以其父类名作为子类名后缀。

![IOSystem](C:/doc/Markdown文档/Java/IOSystem.png)

3\. 节点流和处理流

① 节点流：直接从数据源或目的地读写数据

举例: `FileInputStream/FileOutputSteam	FileReader/FileWriter`

② 处理流：不直接连接到数据源或目的地，而是“连接” 在已存在的流（节点流或处理流）之上，通过对数据的处理为程序提供更为强大的读写功能。

简而言之：节点流作用在文件上，而处理流作用在流上

4\. InputStream 和 Reader

- InputStream 和 Reader 是所有输入流的基类
- InputStream （典型实现 FileInputStream）

  - int read()
  - int read(byte[] b)
  - int read(byte[] b, int off, int len)
  - 这里的 off 是偏移量。从索引为 off 开始存放。`b[off]b[off+1]…`
- Reader （典型实现: FileReader)

  - int read()

  - int read(char [] c)

  - int read(char [] c, int off, int len)
- **程序中打开的文件 IO 资源不属于内存里的资源，垃圾回收机制无法回收该资源，所以应该显式关闭文件 IO 资源 。**
- FileInputStream 从文件系统中的某个文件中获得输入字节。 用于读取非文本数据之类的原始字节流。要读取字符流需要使用 FileReader

*InputStream*

- int read()
  从输入流中读取数据的下一个字节。 返回 0 到 255 范围内的 int 字节值 。如果因为已经到达流末尾而没有可用的字节 则返回值 -1 。

- int read(byte[] b) 

  从此输入流中将最多 b.length 个字节的数据读入一个 byte 数组中。如果因为已经到达流末尾而没有可用的字节 则返回值 1 。 否则**以整数形式返回实际读取的字节数** 。

- int read(byte[] b, int off,int len)

  将输入流中最多 len 个数据字节读入 byte 数组 。 尝试读取 len 个字节 但读取的字节也可能小于该值 。 以整数形式返回实际读取的字节数 。 如果因为流位于文件末尾而没有可用的字节 则返回值 -1 。

  off: 数组b中写入数据的起始偏移量. b[off],b[off+1]…

- public void close() throws IOException
  关闭此输入流并释放与该流关联的所有系统资源 。

*Reader*

- int read()
  读取单个字符。 作为整数读取的字符范围在 0 到 65535 之间 0x0000~0xffff) 2 个
  字节的 Unicode 码如果已到达流的末尾则返回 -1
- int read(char[] cbuf)
  将字符读入数组。如果已到达流的末尾则返回 -1 。 否则返回本次读取的字符数 。
- int read(char[] cbuf,int off,int len)
  将字符读入数组的某一部分。 存到数组 cbuf 中 从 off 处开始存储最多读 len 个字符 。 如果已到达流的末尾则返回 -1 。 否则返回本次读取的字符数 。
- public void close() throws IOException
  关闭此输入流并释放与该流关联的所有系统资源 。

5\. OutputStream 和 Writer

- OutputStream 和 Writer 也非常相似：
  - void write( int b /int c)
  - void write( byte[] b/char[] cbuf)
  - void write( byte[] b/char[] buff, int off, int len)
  - void flush();
  - void close(); 需要先刷新，再关闭此流

因为字符流直接以字符作为操作单位，所以 Writer 可以用字符串来替换字符数组，即以 String 对象作为 参数

- void write(String str)
- void write(String str , int off, int len)

FileOutputStream 从文件系统中的某个文件中获得输出字节 。 FileOutputStream 用于写出非文本 数据之类的原始字节流。 要写出字符流，需要使用 FileWriter

*OutputStream*

- void write(int b)
  将指定的字节写入此输出流。write 的常规协定是：向输出流写入一个字节 。 要写入的字节是参数 b 的八个低位 。 b 的 24 个高位将被忽略 。 即写入 0~255 范围 的 。
- void write(byte[] b)
  将 `b.length` 个字节从指定的 byte 数组写入此输出流。write(b) 的常规协定是：应该与调用 `write(b, 0 b length)` 的效果完全相同 。
- void write(byte[] b,int off,int len)
  将指定 byte 数组中从偏移量 off 开始的 len 个字节写入此输出流。
- public void flush()throws IOException
  刷新此输出流并强制写出所有缓冲的输出字节,调用此方法指示应将这些字节立即写入它们预期的目标 。
- public void close() throws IOException
  关闭此输出流并释放与该流关联的所有系统资源。

Writer

- void write(int c)
  写入单个字符 。 要写入的字符包含在给定整数值的 16 个低位中 16 高位被忽略 。 即写入 0 到 65535 之间的 Unicode 码 。
- void write(char[] cbuf)
  写入字符数组 。
- void write(char[] cbuf,int off,int len)
  写入字符数组的某一部分。 从 off 开始 写入 len 个字符
- void write(String str)
  写入字符串
- void write(String str,int off,int len)
  写入字符串的某一部分
- void flush()
  刷新该流的缓冲则立即将它们写入预期目标 。
- public void close() throws IOException)
  关闭此输出流并释放与该流关联的所有系统资源。

##### 3. 节点流或文件流

1\. 读取文件：

步骤：

① 建立一个流对象，将已存在的一个文件加载进流。

② 创建一个临时存放数据的容器

③ 调用流对象的读取方法将流中的数据读入到数组中。

④ 关闭资源。

```java
		//1. 实例化 File 类的对象,指明要操作的文件
        File file = new File("hello.txt");
        //2. 提供具体的流对象，将已存在的文件对象作为参数传入
        FileReader fileReader = new FileReader(file);
        //3. 数据的读入
		//方式一
        int read = fileReader.read();//调用流对象的读取方法将流中的数据读入
        while(read != -1){//如果在这里产生一个异常，那么就会产生一个 Exception 对象，并将其抛出，那么下面的代码就不会执行了。那么上面所造的流没有关闭。因此这里不适合用 throws
            System.out.print((char) read);
            read = fileReader.read();
        }
        //方式二：语法上针对方式一的优化修改
        /**int read;
          *while( (read = fileReader.read())!= -1 ){
          *    System.out.print((char)read);
          *}
          */
        //4. 流的关闭操作
        fileReader.close();
//缺点: 一个一个字符读取速度较慢
//完整版
    FileReader fileReader = null;
        try {
            //1. 实例化 File 类的对象,指明要操作的文件
            File file = new File("hello.txt");
            //2. 提供具体的流，并指明读取写入数据的终点
            fileReader = new FileReader(file);
            //3. 数据的读入
            int read; // = fileReader.read();
            while((read = fileReader.read())!= -1){
                System.out.print((char) read);
//                read = fileReader.read();
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            
            //4. 流的关闭操作
            //方式一
            try {
                if(fileReader != null) fileReader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            //方式二
             if(fr != null){
                try {
                    fr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
```

一个一个获取字符速度过慢因此我们提供一个容器来进行缓冲。从而提高速度。对 read() 操作升级：使用 read 的重载方法

```java
FileReader fr = null;
        try {
            //1.File类的实例化
            File file = new File("hello.txt");

            //2.FileReader流的实例化
            fr = new FileReader(file);

            //3.读入的操作
            //read(char[] cbuf):返回每次读入cbuf数组中的字符的个数。如果达到文件末尾，返回-1
            char[] cbuf = new char[5];
            int len;
            while((len = fr.read(cbuf)) != -1){
                //方式一：
                //错误的写法
//                for(int i = 0;i < cbuf.length;i++){
//                    System.out.print(cbuf[i]);
//                }
                //正确的写法
//                for(int i = 0;i < len;i++){
//                    System.out.print(cbuf[i]);
//                }
                //方式二：
                //错误的写法,对应着方式一的错误的写法
//                String str = new String(cbuf);
//                System.out.print(str);
                //正确的写法
                String str = new String(cbuf,0,len);
                System.out.print(str);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if(fr != null){
                //4.资源的关闭
                try {
                    fr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
        }
/*
    说明点：
    1. read()的理解：返回读入的一个字符。如果达到文件末尾，返回 -1
    2. 异常的处理：为了保证流资源一定可以执行关闭操作。需要使用try-catch-finally处理
    3. 读入的文件一定要存在，否则就会报FileNotFoundException。
     */
```

> 程序中打开的文件 IO 资源不属于内存里的资源，垃圾回收机制无法回收该资源，所以应该显式关闭文件 IO 资源 。

为什么这里要使用 try-catch-finally 来处理异常而不是用 throws 直接抛出?

无论上面的代码是否有异常，我们的都是要关闭流。否则会造成资源泄露，内存消耗等问题。因此要使用 try-catch-finally

2\. 将内存中的数据输出到文件

说明：

① 输出操作，对应的 File 可以不存在的。并不会报异常
② File 对应的硬盘中的文件如果不存在，在输出的过程中，会自动创建此文件。 
	File 对应的硬盘中的文件如果存在：           
		如果流使用的构造器是：FileWriter(file,false) / FileWriter(file):对原有文件的覆盖            
		如果流使用的构造器是：FileWriter(file,true):不会对原有文件覆盖，而是在原有文件基础上追加内容
		在写入一个文件时 ，如果使用构造器 `FileOutputStream(file)/FileOutputStream(file,false)`，则目录下有同名文件将被覆盖。
		如果使用构造器 `FileOutputStream(file,true)`，则目录下的同名文件不会被覆盖
在文件内容末尾追加内容。

③ 字符流操作字符，只能操作普通文本文件 。 最常见的文本文件： `.txt, .java, .c, .cpp` 等语言 的 源代码。尤其注意 .doc,excel,ppt 这些不是文本文件。

```java
FileWriter fw = null;
        try {
            //1.提供File类的对象，指明写出到的文件
            File file = new File("hello1.txt");

            //2.提供FileWriter的对象，用于数据的写出
            fw = new FileWriter(file,false);

            //3.写出的操作
            fw.write("I have a dream!\n");
            fw.write("you need to have a dream!");
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //4.流资源的关闭
            if(fw != null){

                try {
                    fw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
           }
        }
```

使用 FileReader 和 FileWriter 实现文本文件的复制：

```java
		FileReader fr = null;
        FileWriter fw = null;
        try {
            //1.创建File类的对象，指明读入和写出的文件
            File srcFile = new File("hello.txt");
            File destFile = new File("hello2.txt");

            //不能使用字符流来处理图片等字节数据
//            File srcFile = new File("爱情与友情.jpg");
//            File destFile = new File("爱情与友情1.jpg");


            //2.创建输入流和输出流的对象
            fr = new FileReader(srcFile);
            fw = new FileWriter(destFile);


            //3.数据的读入和写出操作
            char[] cbuf = new char[5];
            int len;//记录每次读入到cbuf数组中的字符的个数
            while((len = fr.read(cbuf)) != -1){
                //每次写出len个字符
                fw.write(cbuf,0,len);

            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //4.关闭流资源
            //方式一：
//            try {
//                if(fw != null)
//                    fw.close();
//            } catch (IOException e) {
//                e.printStackTrace();
//            }finally{
//                try {
//                    if(fr != null)
//                        fr.close();
//                } catch (IOException e) {
//                    e.printStackTrace();
//                }
//            }
            //方式二：
            try {
                if(fw != null)
                    fw.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
```

注意：定义文件路径时，可以用 `/ 或 \\`

**FileInputStream 和 FileOutputStream**

使用字节流 FileInputStream 处理文本文件，可能出现乱码。但是可以用来复制文本文件

```java
 FileInputStream fis = null;
        try {
            //1. 造文件
            File file = new File("hello.txt");

            //2.造流
            fis = new FileInputStream(file);

            //3.读数据
            byte[] buffer = new byte[5];
            int len;//记录每次读取的字节的个数
            while((len = fis.read(buffer)) != -1){

                String str = new String(buffer,0,len);
                System.out.print(str);

            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if(fis != null){
                //4.关闭资源
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
        }
```

##### 4. 缓冲流

- 为了提高数据的读写速度，Java API 提供了带缓冲功能的流类，在使用这些流类
  时，会创建一个内部缓冲区数组，缺省使用 8192 个 字节 8Kb) 的缓冲区 。

  ```java
  private static int DEFAULT_BUFFER_SIZE = 8192;
  ```

- 缓冲 流要“套接”在相应的节点流之上，根据 数据操作单位可以把缓冲流分为：

  - BufferedInputStream 和 BufferedOutputStream

  - BufferedReader 和 BufferedWriter

  使用方法 flush() 可以强制将缓冲区的内容全部写入输出流

- 关闭流的顺序和打开流的顺序相反。只要关闭最外层流 即可， 关闭最外层流也会相应关闭内层节点流

- flush() 方法的 使用：手动将 buffer 中内容写入文件

- 如果是带缓冲区的流对象的 close() 方法，不但会关闭流还会在关闭流之前刷
  新缓冲区关闭后不能再写出

![缓冲流与非缓冲流的区别](C:/doc/Markdown文档/Java/缓冲流与非缓冲流的区别.png)

**缓冲流(字节流)实现非文本文件的复制**

```java
    BufferedInputStream bis = null;
        BufferedOutputStream bos = null;

        try {
            //1.造文件
            File srcFile = new File("爱情与友情.jpg");
            File destFile = new File("爱情与友情3.jpg");
            //2.造流
            //2.1 造节点流
            FileInputStream fis = new FileInputStream((srcFile));
            FileOutputStream fos = new FileOutputStream(destFile);
            //2.2 造缓冲流
            bis = new BufferedInputStream(fis);
            bos = new BufferedOutputStream(fos);

            //3.复制的细节：读取、写入
            byte[] buffer = new byte[10];
            int len;
            while((len = bis.read(buffer)) != -1){
                bos.write(buffer,0,len);

//                bos.flush();//刷新缓冲区

            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //4.资源关闭
            //要求：先关闭外层的流，再关闭内层的流
            if(bos != null){
                try {
                    bos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
            if(bis != null){
                try {
                    bis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
            //说明：关闭外层流的同时，内层流也会自动的进行关闭。关于内层流的关闭，我们可以省略.
//        fos.close();
//        fis.close();
        }
```

练习：

实现非文本文件的加密和解密：

```java
    //图片的加密
    @Test
    public void test1() {

        FileInputStream fis = null;
        FileOutputStream fos = null;
        try {
            fis = new FileInputStream("爱情与友情.jpg");
            fos = new FileOutputStream("爱情与友情secret.jpg");

            byte[] buffer = new byte[20];
            int len;
            while ((len = fis.read(buffer)) != -1) {
                //字节数组进行修改
                //错误的
                //            for(byte b : buffer){
                //                b = (byte) (b ^ 5);
                //            }
                //正确的
                for (int i = 0; i < len; i++) {
                    buffer[i] = (byte) (buffer[i] ^ 5);
                }


                fos.write(buffer, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fos != null) {
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
            if (fis != null) {
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
        }


    }


    //图片的解密
    @Test
    public void test2() {

        FileInputStream fis = null;
        FileOutputStream fos = null;
        try {
            fis = new FileInputStream("爱情与友情secret.jpg");
            fos = new FileOutputStream("爱情与友情4.jpg");

            byte[] buffer = new byte[20];
            int len;
            while ((len = fis.read(buffer)) != -1) {
                //字节数组进行修改
                //错误的
                //            for(byte b : buffer){
                //                b = (byte) (b ^ 5);
                //            }
                //正确的
                for (int i = 0; i < len; i++) {
                    buffer[i] = (byte) (buffer[i] ^ 5);
                }

                fos.write(buffer, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fos != null) {
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
            if (fis != null) {
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
        }
        
    }
```

获取文本上字符出现的次数,把数据写入文件

```java
/**
 * 练习3: 获取文本上字符出现的次数,把数据写入文件
 *
 * 思路：
 * 1. 遍历文本每一个字符
 * 2. 字符出现的次数存在Map中
 *
 * Map<Character,Integer> map = new HashMap<Character,Integer>();
 * map.put('a',18);
 * map.put('你',2);
 *
 * 3.把map中的数据写入文件
 *
 * @author shkstart
 
 * @create 2019 下午 3:47
 */
	    FileReader fr = null;
        BufferedWriter bw = null;
        try {
            //1.创建Map集合
            Map<Character, Integer> map = new HashMap<Character, Integer>();

            //2.遍历每一个字符,每一个字符出现的次数放到map中
            fr = new FileReader("dbcp.txt");
            int c = 0;
            while ((c = fr.read()) != -1) {
                //int 还原 char
                char ch = (char) c;
                // 判断char是否在map中第一次出现
                if (map.get(ch) == null) {
                    map.put(ch, 1);
                } else {
                    map.put(ch, map.get(ch) + 1);
                }
            }

            //3.把map中数据存在文件count.txt
            //3.1 创建Writer
            bw = new BufferedWriter(new FileWriter("wordcount.txt"));

            //3.2 遍历map,再写入数据
            Set<Map.Entry<Character, Integer>> entrySet = map.entrySet();
            for (Map.Entry<Character, Integer> entry : entrySet) {
                switch (entry.getKey()) {
                    case ' ':
                        bw.write("空格=" + entry.getValue());
                        break;
                    case '\t'://\t表示tab 键字符
                        bw.write("tab键=" + entry.getValue());
                        break;
                    case '\r'://
                        bw.write("回车=" + entry.getValue());
                        break;
                    case '\n'://
                        bw.write("换行=" + entry.getValue());
                        break;
                    default:
                        bw.write(entry.getKey() + "=" + entry.getValue());
                        break;
                }
                bw.newLine();
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //4.关流
            if (fr != null) {
                try {
                    fr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
            if (bw != null) {
                try {
                    bw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
        }
```


##### 5. 转换流

- 转换流提供了字节流和字符流之间的转换
- Java API 提供了两个转换流：
  - InputStreamReader: 将 InputStream 转换为 Reader 
  - OutputStreamWriter: 将 Writer 转换为  OutputStream (将一个字符的输出流转换为一个字节的输出流)
- 字节流中的数据都是字符时，转换为字符里更为高效
- 很多时候我们使用转换流来处理文件乱码问题。实现编码和
  解码的功能。

**InputStreamReader**

- 实现酱字节的输入流按指定字符集转换为字符的输入流
- 需要和 InputStream 连接

构造器

- public InputStreamReader(InputStream in)
- public InputStreamReader(InputStream in,String charsetName)

如：Reader isr = new InputStreamReader(System.in,'gbk');

**OutputStreamWriter**

- 实现将字符的输出流按指定字符集转换为字节的输出流
- 需要和 OutputStream "套接"

构造器

- public OutputStreamWriter(OutputStream out)
- public OutputStreamWriter(OutputStream out,String charsetName)

```java
        BufferedReader br = null;
        BufferedWriter bw = null;
        try {
            Charset gbk = Charset.forName("GBK");
            File file = new File("C:\\Users\\XJ\\Desktop\\Python.md");
            FileInputStream fileInputStream = new FileInputStream(file);
            FileOutputStream fileOutputStream = new FileOutputStream("hello.md");
            InputStreamReader isr = new InputStreamReader(fileInputStream,"UTF-8");
            OutputStreamWriter osw = new OutputStreamWriter(fileOutputStream,gbk);
            br = new BufferedReader(isr);
            bw = new BufferedWriter(osw);
            String str = null;
            while((str = br.readLine()) != null){
                bw.write(str);
                bw.newLine();
                bw.flush();
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if(br != null) {
                try {
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(bw != null) {
                try {
                    bw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
```

##### 6. 标准输入输出流

- System.in 和 System.out 分别代表了系统标准的输入和输出设备
- 默认输入设备是键盘，输出设备是：显示器
- System.in 的类型是 InputStream
- System.out 的类型是 PrintStream ，其是 OutputStream 的子类，FilterOutputStream 的子类
- System 类的 setIn()/setOut 方式重新指定输入输出的流。
  - public static void setIn(InputStream in)
  - public static void setOut(PrintStream out)

输入步骤：

```java
//从键盘输入字符串，要求将读取到的整行字符串转成大写输出。然后继续进行输入操作，直至当输入“ e ”或者 exit ”时，退出程序。
        System.out.println("请输入信息，退出输入 e 或 exit");
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String s = null;
        try {
            while((s = br.readLine()) != null){
                if("e".equalsIgnoreCase(s) || "exit".equalsIgnoreCase(s)){
                    System.out.println("安全退出");
                    break;
                }
                System.out.println("-->" + s.toUpperCase());
                System.out.println("继续输出信息");
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if(br != null){
                try {
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```

```java
//创建一个类可以从键盘读取 int,double,float,boolean,short,byte and String values the keyboard

public class MyInput {

    private static final Logger LOGGER = LoggerFactory.getLogger(MyInput.class);

    public static String readString(){
        InputStreamReader isr = new InputStreamReader(System.in);
        BufferedReader br = new BufferedReader(isr);
        String str = "";
        try {
            br.readLine();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return str;
    }

    public static int readInt(){
        return Integer.parseInt(readString());
    }
    public static double readDouble(){
        return Double.parseDouble(readString());
    }
    public static float readFloat(){
        return Float.parseFloat(readString());
    }
    public static Byte readByte(){
        return Byte.parseByte(readString());
    }
    public static short readShort(){
        return Short.parseShort(readString());
    }
    public static long readLong(){
        return Long.parseLong(readString());
    }

}
```



1. 创建 File 类的对象，指明读取数据的来源。(要求此文件一定要存在)

2. 创建对应的输入流，将 File 类的对象作为参数，传入流的构造器中。

3. 具体的读入过程：

   创建相应的 byte[] 或 char[]

4. 关闭流资源

   注意：需要使用 try-catch-finally 来处理

##### 7. 打印流

实现将基本数据类型的数据格式转化为字符串输出。

打印流：PrintStream 和 PrintWriter

- 提供了一系列的 print() 和 println() 方法，用于多种数据类型的输出
- PrintStream 和 PrintWriter 的输出不会抛出 IOException
- PrintStream 和 PrintWriter 有自动 flush 功能
- printStream 打印的所有字符都使用平台默认字符编码，在需要写入字符而不是写入字节的情况下，应该使用 PrintWriter
- System.out 返回的是 PrintStream 的实例

```java
        PrintStream ps = null;
        try {
            FileOutputStream fos = new FileOutputStream(new File("D:\\IO\\text.txt"));
            // 创建打印输出流,设置为自动刷新模式(写入换行符或字节 '\n' 时都会刷新输出缓冲区)
            ps = new PrintStream(fos, true);
            if (ps != null) {// 把标准输出流(控制台输出)改成文件
                System.setOut(ps);
            }


            for (int i = 0; i <= 255; i++) { // 输出ASCII字符
                System.out.print((char) i);
                if (i % 50 == 0) { // 每50个数据一行
                    System.out.println(); // 换行
                }
            }


        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } finally {
            if (ps != null) {
                ps.close();
            }
        }
```

##### 8. 数据流

- 为了方便操作 Java 语言的基本数据类型和 String 的数据，可以使用数据流
- 作用：定义基本数据类型和 String 的数据
- 数据流有两个类：(用于读取和写出基本数据类型、String 类型的数据)
  - DataInputStream 和 DataOutputStream
  - 分别 “套接” 在 IntputStream 和 OutputStream 的流上
- DataInputStream 中的方法
  - boolean readBoolean()		byte readByte()
  - char readChar()                   float readFloat()
  - double readDouble()          short readShort()
  - long readLong()                  int readInt()
  - String readUTF()                 void readFully(byte[] b)
- DataOutputStream 中的方法
  - 将上述方法的 read 改为 writer 即可

```java
 /*
    3. 数据流
    3.1 DataInputStream 和 DataOutputStream
    3.2 作用：用于读取或写出基本数据类型的变量或字符串

    练习：将内存中的字符串、基本数据类型的变量写出到文件中。

    注意：处理异常的话，仍然应该使用try-catch-finally.
     */
    @Test
    public void test3() throws IOException {
        //1.
        DataOutputStream dos = new DataOutputStream(new FileOutputStream("data.txt"));
        //2.
        dos.writeUTF("刘建辰");
        dos.flush();//刷新操作，将内存中的数据写入文件
        dos.writeInt(23);
        dos.flush();
        dos.writeBoolean(true);
        dos.flush();
        //3.
        dos.close();


    }
  /*
    将文件中存储的基本数据类型变量和字符串读取到内存中，保存在变量中。

    注意点：读取不同类型的数据的顺序要与当初写入文件时，保存的数据的顺序一致！

     */
    @Test
    public void test4() throws IOException {
        //1.
        DataInputStream dis = new DataInputStream(new FileInputStream("data.txt"));
        //2.
        String name = dis.readUTF();
        int age = dis.readInt();
        boolean isMale = dis.readBoolean();

        System.out.println("name = " + name);
        System.out.println("age = " + age);
        System.out.println("isMale = " + isMale);

        //3.
        dis.close();

    }
```



##### 9. 对象流 ObjectInputStream 和 ObjectOutputStream

- **用于存储和读取基本数据类型或对象的处理流，它的强大之处可以把 Java 中的对象写入到数据源中，也能把对象从数据源中还原出来**
- 序列化：用 OjbectOutputStream 类保存基本类型数据或对象的机制
- 反序列化：用 OjbectInputStream 类读取基本类型数据或对象的机制
- ObjectOutputStream 和 ObjectInputStream 不能序列化 static 和 trnsient 修饰的成员变量

> static 和 transient 修饰的成员不能够序列。也就是在生成的对象时，值不能保存。例如: static 修饰的变量的是所有共享公用的，保存在类中（静态方法区）

对象的序列化：

- **对象序列化机制**：允许把内存中的 Java 对象转换成平台无关的二进制流，从而把这种二进制流持久地保存在磁盘上，或通过网络将这种二进制流传输到另一个网络节点 。 当其它程序获取了这种二进制流，就可以恢复成原来的 Java 对象
- 序列化的好处在于可将任何实现了 Serializable 接口的对象转化为字节数据使其在保存和传输后可被还原
- 序列化是 RMI (Remote Method Invoke 远程方法调用）过程的参数和返回值都必须实现的机制，而 RMI 是 JavaEE 的基础。因此序列化机制是 JavaEE 平台的基础
- 如果需要让某个对象支持序列化机制，则必须让对象所属的类及其属性是可序列化的，为了让某个类是可序列化的，该类必须实现如下两个接口 之一。否则，会抛出 NotSerializableException 异常
  - Serializable
  - Externalizable

```java
/**
 * Person需要满足如下的要求，方可序列化
 * 1.需要实现接口：Serializable/Externalizable
 * 2.当前类提供一个全局常量：serialVersionUID
 * 3.除了当前 Person 类需要实现 Serializable 接口之外，还必须保证其内部所有属性
 *   也必须是可序列化的。（默认情况下，基本数据类型可序列化）
 *
 *
 * 补充：ObjectOutputStream和ObjectInputStream不能序列化static和transient修饰的成员变量
 *
 *
 * @author shkstart
 * @create 2019 上午 10:38
 */
public class Person implements Serializable{

    public static final long serialVersionUID = 475463534532L;

    private String name;
    private int age;
    private int id;
    private Account acct;

    public Person(String name, int age, int id) {
        this.name = name;
        this.age = age;
        this.id = id;
    }

    public Person(String name, int age, int id, Account acct) {
        this.name = name;
        this.age = age;
        this.id = id;
        this.acct = acct;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", id=" + id +
                ", acct=" + acct +
                '}';
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public Person(String name, int age) {

        this.name = name;
        this.age = age;
    }

    public Person() {

    }
}

class Account implements Serializable{
    public static final long serialVersionUID = 4754534532L;
    private double balance;

    @Override
    public String toString() {
        return "Account{" +
                "balance=" + balance +
                '}';
    }

    public double getBalance() {
        return balance;
    }

    public void setBalance(double balance) {
        this.balance = balance;
    }

    public Account(double balance) {

        this.balance = balance;
    }
}

    
    
 //序列化过程：将内存中的 Java 的对象保存到磁盘、数据库中或通过网络传输出去，使用 ObjectOutputStream 实现
@Test
    public void testObjectOutputStream(){
        ObjectOutputStream oos = null;

        try {
            //1.
            oos = new ObjectOutputStream(new FileOutputStream("object.dat"));
            //2.
            oos.writeObject(new String("我爱北京天安门"));
            oos.flush();//刷新操作

            oos.writeObject(new Person("王铭",23));
            oos.flush();

            oos.writeObject(new Person("张学良",23,1001,new Account(5000)));
            oos.flush();

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if(oos != null){
                //3.
                try {
                    oos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
        }

    }


    /*
    反序列化：将磁盘文件中的对象还原为内存中的一个 java 对象
    使用ObjectInputStream来实现
     */
    @Test
    public void testObjectInputStream(){
        ObjectInputStream ois = null;
        try {
            ois = new ObjectInputStream(new FileInputStream("object.dat"));

            Object obj = ois.readObject();
            String str = (String) obj;

            Person p = (Person) ois.readObject();
            Person p1 = (Person) ois.readObject();

            System.out.println(str);
            System.out.println(p);
            System.out.println(p1);

        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } finally {
            if(ois != null){
                try {
                    ois.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
        }
    }
```

3. 需要满足如下的要求，方可序列化：
   1. 需要实现接口: Serializable
   2. 当前类提供一个全局常量：long serialVersionUID //提供标识，网络中有可能存在多个二进制流，需要对对象还原，通过这个标识来判断属于哪个类。
   3. 除了当前类需要实现 serializable 接口之外，还必须保证其内部所有属性也必须是可序列化的。(默认情况下基本数据类型可序列化)
4. 注意写出一次，操作 flush() 一次

**对象的序列化**

序列化版本标识符

- private static final long serialVersionUID;
- serialVersionUID: 用来*表明类的不同版本间的兼容性*。 简言之，其目的是以序列化对象进行版本控制，有关各版本反序列化时是否兼容。
- 如果类没有显式定义这个静态常量，它的值会根据类的内部细节会自动生成。若类的实例变量发生了修改，serialVersionUID 可能会发生变化（会导致反序列化时找不到对应的类）。故建议显式声明

Java 的序列化机制是通过在运行时判断类的 serialVersionUID 来验证版本一致性的。在进行反序列化时， JVM 会把传来的字节流中的 serialVersionUID 与本地相应实体类的 ，serialVersionUID 进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的异常。

##### 10. 随机存取文件流

- RandomAccessFile 声明在 java.io 包下，直接继承于 java.lang.Object 类。 并且它实现了 DataInput 、 DataOutput 这两个接口，也就意味着这个类既可以读也可以写。

- RandomAccessFile 类支持 "随机访问"  的方式，程序可以直接跳到文件的任意地方来读、写文件

  - 支持只访问文件的部分内容
  - 可以向已存在的文件后追加内容

- RandomAccessFile 对象包含一个记录指针，用以表示当前读写的位置

  RandomAccessFile 类对象可以有自由移动记录指针

  - long getFilePointer(): 获取文件记录指针的当前位置
  - void seek(long pos): 将文件记录指针定位到 pos 位

- 构造器

  - public RandomAccessFile(File file , String mode)
  - public RandomAccessFile (String name, String mode）

- 创建 RandomAccessFile 类实例需要指定一个 mode 参数，该参数指定 RandomAccessFile 的访问模式：
      - r: 以只读方式打开
      - rw ：打开以便读取和写入
      - rwd 打开以便读取和 写入；同步文件内容的更新
      - rws 打开以便读取和 写入； 同步文件内容和元数据的更新

- 如果模式为只读 r 。则不会创建文件，而是会去读取一个已经存在的文件，如果读取的文件不存在则会出现异常。 如果模式为 rw 读写。如果文件不存在则会去创建文件，如果存在则不会创建。

注：每次 “write” 数据时，“rw” 模式，数据不会立即写到硬盘中；而 “rwd” 数据会被立即写入到硬盘。如果写数据过程中发生异常，“rwd” 摸下已被 write 的数据会被保存到硬盘，而 “rw” 则全部丢失。

```java
    /**
    *2. RandomAccessFile既可以作为一个输入流，又可以作为一个输出流
    *3. 如果 RandomAccessFile  作为输出流时，写出到的文件如果不存在，则在执行过程中	自动创建；如果写出到的文件存在，则会对源文件的内容进行覆盖。(默认情况下从头覆盖)
	*4. 可以通过相关操作实现 RandomAccessFile 数据插入的效果
    */
	@Test
    public void test1() {

        RandomAccessFile raf1 = null;
        RandomAccessFile raf2 = null;
        try {
            //1.
            raf1 = new RandomAccessFile(new File("爱情与友情.jpg"),"r");
            raf2 = new RandomAccessFile(new File("爱情与友情1.jpg"),"rw");
            //2.
            byte[] buffer = new byte[1024];
            int len;
            while((len = raf1.read(buffer)) != -1){
                raf2.write(buffer,0,len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //3.
            if(raf1 != null){
                try {
                    raf1.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
            if(raf2 != null){
                try {
                    raf2.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
        }
    }

    @Test
    public void test2() throws IOException {

        RandomAccessFile raf1 = new RandomAccessFile("hello.txt","rw");

        raf1.seek(3);//将指针调到角标为3的位置
        raf1.write("xyz".getBytes());//

        raf1.close();

    }
    /*
    使用RandomAccessFile实现数据的插入效果
     */
    @Test
    public void test3() throws IOException {

        RandomAccessFile raf1 = new RandomAccessFile("hello.txt","rw");

        raf1.seek(3);//将指针调到角标为3的位置
        //保存指针3后面的所有数据到StringBuilder中
        StringBuilder builder = new StringBuilder((int) new File("hello.txt").length());
        byte[] buffer = new byte[20];
        int len;
        while((len = raf1.read(buffer)) != -1){
            builder.append(new String(buffer,0,len)) ;
        }
        //调回指针，写入“xyz”
        raf1.seek(3);
        raf1.write("xyz".getBytes());

        //将StringBuilder中的数据写入到文件中
        raf1.write(builder.toString().getBytes());

        raf1.close();

        //思考：将StringBuilder替换为ByteArrayOutputStream
    }
```

我们可以用 RandomAccessFile 这个类，来实现一个 多线程断点下载 的功能，
用过下载工具的朋友们都知道，下载前都会建立 两个临时文件 ，一个是与
被下载文件大小相同的空文件，另一个是记录文件指针的位置文件，每次
暂停的时候，都会保存上一次的指针，然后断点下载的时候，会继续从上
一次的地方下载，从而实现断点下载或上传的功能，有兴趣的朋友们可以
自己实现下。

##### 11. NIO 中的 Path、Paths、File 类的使用

- Java NIO (New IO Non Blocking IO) 是从 Java 1.4 版本开始引入的一套新的 IO API ，可以替代标准的 Java IO API 。 NIO 与原来的 IO 有同样的作用和目的，但是使用的方式完全不同， NIO 支持面向缓冲区的 (IO 是面向流的 、基于通道的 IO 操作。 NIO 将以更加高效的方式进行文件的读写操作。
- Java API 中提供了两套 NIO 一套是针对标准输入输出 NIO 另一套就是网
  络编程 NIO
  - java.nio.channels.Channel
    - FileChannel: 处理本地文件
    - SocketChannel TCP 网络编程的客户端的 Channel
    - ServerSocketChannel:TCP 网络编程的服务器端的 Channel
    - DatagramChannel UDP 网络编程中发送端和接收端的 Channel

**NIO 2.0** JDK 7 发布

- 早期 的 Java 只提供了一个 File 类来访问文件系统，但 File 类的功能有限，所提供的方法性能也不高。而且， 大多数方法在出错时仅返回失败，并不会提供异常信息。

- NIO. 2 为了弥补这种不足，引入了 Path 接口，代表一个平台无关的平台路径，描
  述了目录结构中文件的位置。 Path 可以看成是 File 类的升级版本，实际引用的资
  源也可以不存在。

- 在以前 IO 操作都是这样写的

  ```java
  import java.io.File;
  File file = new File("index.html");
  
  ```

- 但在 Java7 中，我们可以这样写：

  ```java
  import java.nio.file.Path;
  import java.nio.file.Paths;
  Path path = Paths.get("index.html");
  ```

Path 和 Paths

Paths 则包含了两个返回 Path 的静态工厂方法。

- Paths 类提供的静态 get() 方法用来获取 Path 对象：
  - `static Path get(String first, String … more)` : 用于将多个字符串串连成路径
  - `static Path get(URI uri)`: 返回指定 uri 对应的 Path 路径
- Path 常用方法：
  String toString() 返回调用 Path 对象的字符串表示形式
  boolean startsWith(String path) : 判断是否以 path 路径开始
  boolean endsWith(String path) : 判断是否以 path 路径结束
  boolean isAbsolute() : 判断是否是绝对路径
  Path getParent() ：返回 Path 对象包含整个路径，不包含 Path 对象指定的文件路径
  Path getRoot() ：返回调用 Path 对象的根路径
  Path getFileName() : 返回与调用 Path 对象关联的文件名
  int getNameCount() : 返回 Path 根目录后面元素的数量
  Path getName(int idx) : 返回指定索引位置 idx 的路径名称
  Path toAbsolutePath() : 作为绝对路径返回调用 Path 对象
  Path resolve(Path p) : 合并两个路径，返回合并后的路径对应的 Path 对象
  File toFile(): 将 Path 转化为 File 类的对象

**Files 类**

java.nio.file.Files 用于操作文件或目录的工具类。

**Files** 常用方法：

- Path copy(Path src, Path dest, CopyOption … how) : 文件的复制
- Path createDirectory(Path path, FileAttribute<?> … attr) : 创建一个目录
- Path createFile(Path path, FileAttribute<?> … arr) : 创建一个文件
- void delete(Path path) : 删除一个文件 目录，如果不存在，执行报错
- void deleteIfExists(Path path) : Path 对应的文件 目录如果存在，执行删除
- Path move(Path src, Path dest, CopyOption…how) : 将 src 移动到 dest 位置
- long size(Path path) : 返回 path 指定文件的大小
  **Files 常用方法：用于判断**
- boolean exists(Path path, LinkOption … opts) : 判断文件是否存在
- boolean isDirectory(Path path, LinkOption … opts) : 判断是否是目录
- boolean isRegularFile(Path path, LinkOption … opts) : 判断是否是文件
- boolean isHidden(Path path) : 判断是否是隐藏文件
- boolean isReadable(Path path) : 判断文件是否可读
- boolean isWritable(Path path) : 判断文件是否可写
- boolean notExists(Path path, LinkOption … opts) : 判断文件是否不存在

**Files 常用方法：用于操作内容**

- SeekableByteChannel newByteChannel(Path path, OpenOption…how) : 获取与指定文件的连接， how 指定打开方式。
- DirectoryStream<Path> newDirectoryStream(Path path) : 打开 path 指定的目录
- InputStream newInputStream(Path path, OpenOption…how): 获取 InputStream 对象
- OutputStream newOutputStream(Path path, OpenOption…how) : 获 OutputStream对象

##### 12. 使用第三方 jar 包实现数据读写

```xml
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.8.0</version>
</dependency>
```

```java
         File srcFile = new File("day10\\爱情与友情.jpg");
        File destFile = new File("day10\\爱情与友情2.jpg");

        try {
            FileUtils.copyFile(srcFile,destFile);
        } catch (IOException e) {
            e.printStackTrace();
        }
```

### 第七章 网络编程

##### 1. 网络编程概述

- 目的：直接或间接的使用网络协议与其它计算机实现数据交换、进行通讯
- 两个问题：
  - 如何准确定位网络上一台或多台主机：定位主机上特定的应用
  - 找到主机后如何可靠高效地进行数据传输
- 通信双方地址：
  - IP
  - 端口号
- 一定的规则 (即：网络通信协议)
  - OSI 参考模型：模型过于理想化，未能在因特网上广泛推广
  - TCP/IP 参考模型(TCP/IP 协议)：事实上的国际标准。

##### 2. 网络通信要素1 ：IP 和端口号

1. Internet 上计算机的唯一标识
2. 在 Java 中使用 InetAddress 代表 IP
3. IP 分类: IPv4 和 IPv6; 公网地址(万维网使用)和私有地址(局域网使用)；

- 本地回路地址 hostAddress: 127.0.0.1 主机名hostName: localhost:	

4. 实例化 InetAddress 的两个方法：`getByName(String host)`,获取本机地址`getLocalhost()`

   getHostName() / getHostAddress

> - 端口号标识计算机上运行的进程(程序)
>   - 不同的进程有不同的端口号
>   - 被规定一个 16 位的整数 0~65535
>   - 端口分类：
>     - 公认端口：0~1023。 被预先定义的服务通信占用。exp: `FTP 21\HTTP 80\Telent 23\…`
>     - 注册端口：1024~49151。分配给用户进程或应用程序。exp:`MySQL 3306\Oracle 1521\Tomcat 8080\…`
>     - 动态\私有端口：49152~65535 
> - 端口号 + IP 地址 = 网络嵌套字。Socket

##### 3. 通信要素2：网络协议

计算机网络中实现通信必须有一些约定，即通信协议。

**TCP/IP 协议**

- 传输层两个非常重要的协议：UDP/TCP
- TCP/IP:因 TCP 传输控制协议和 IP 网络互联协议而得名。 实际上是一组协议，包括多个具有不同功能且相互关联的协议。
- IP (Internet Protocol) 协议是网络层的主要协议，支持网络间的数据通信

TCP 协议：

> - 使用 TCP 协议前，须先建立 TCP 连接，形成传输数据通道
> - 传输前，采用“ 三次握手 方式 ，点对点通信是可靠的
> - TCP 协议进行通信的两个应用进程：客户端、 服务端。
> - 在连接中可进行大数据量的传输
> - 传输完毕，需释放已建立的连接效率低

UDP 协议

> - 将数据、源、目的封装成数据包， 不需要建立连接
> - 每个数据报的大小限制在 64K 内
> - 发送不管对方是否准备好，接收方收到也不确认， 故是不可靠 的
> - 可以广播发送
> - 发送数据结束时 无需释放 资源 ，开销小，速度 快

##### 4. TCP 网络编程

```java
public class TCPtest {
    @Test
    public void client(){
        Socket socket = null;
        OutputStream os = null;
        try {
            InetAddress ip = InetAddress.getByName("127.0.0.1");
            socket = new Socket(ip,46231);
            os = socket.getOutputStream();
            os.write("你好我是客户端".getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        } finally {

            if (os != null) {
                try {
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    @Test
    public void server(){

        ServerSocket ss = null;
        Socket socket = null;
        InputStream is = null;
        ByteArrayOutputStream baos = null;
        try {
            ss = new ServerSocket(46231);
            socket = ss.accept();
            is = socket.getInputStream();
            baos = new ByteArrayOutputStream();

            byte[] bbufer = new byte[10];
            int len;
            while((len = is.read(bbufer)) != -1){
                baos.write(bbufer,0,len);
            }
            System.out.println(baos.toString());
            System.out.println("消息来自"+socket.getInetAddress().getHostAddress());
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if(baos != null)baos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if(is != null) is.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if(socket != null)socket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if(ss != null) ss.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

客户端：

1. 建立 Socket 对象，指明服务器端 IP 和端口号
2. 获取输出流，用以输出数据
3. 写出数据的操作。
4. 资源的关闭

服务器：

1. 创建服务器端的 socket. ServerSocket,指明自己的端口号
2. 调用 accept() 表示接受客户端的 Socket
3. 获取输入流，从输入流中读取数据
4. 资源的关闭

##### 5. UDP 网络编程

- DatagramSocket 和 DatagramPacket 实现基于 UDP 网络程序
- DatagramPacket 对象封装了 UDP 数据报，在数据报包含了发送端的 IP 地址、端口号和接受端的IP 地址和端口号。

例子：

```java
    public void send() throws IOException {
        DatagramSocket socket   = new DatagramSocket();
        String str = "发送导弹";
        byte[] data = str.getBytes();
        DatagramPacket packet = new DatagramPacket(data, 0, data.length, InetAddress.getLocalHost(), 12323);
        socket.send(packet);
        socket.close();
    }

    @Test
    public void receive() throws IOException {
        DatagramSocket socket = new DatagramSocket(12323);
        byte[] buffer = new byte[128];
       DatagramPacket packet = new DatagramPacket(buffer,0,buffer.length);
        socket.receive(packet);
//        int len = 0;
        //packet.getData() 获取 packet 中的数组
        //packet.getlength() 获取输入的字节数
        System.out.println(new String(packet.getData(),0,packet.getLength()));

    }
```

##### 6. URL 编程

- URL (Uniform Resource Locator) : 统一资源定位符，它表示 Internet 上某一资源的地址
- 它是一种具体的 URI，即 URL 可以用来表示一个资源，而且还指明了 Locate 这个资源
- 通过 URL 我们可以访问 Internet 上的各种网络资源，比如最常见的 www\FTP 站点。浏览器通过解析给定的 URL 在网络查找相应的资源和其它文件
- URL 的基本结构由 5 部分组成：
  - <传输协议>：//<主机名>:<端口号>/<文件名（包含资源所在地址）>#片段名?参数列表
  - 例如：http://192.168.1.100:8080/helloworld/index.jsp#a?username=shkstart&password=123
  - #片段名：即锚点（可直接定位到某个位置）
  - 参数列表格式：参数名=参数值&参数名=参数值……

URL 构造器

为了表示 URL java.net 中实现了类 URL 。我们可以通过下面的构造器来初
始化一个 URL 对象：

- public URL (String spec ))：通过 一个表示 URL 地址的字符串可以构造一个 URL 对象。例如 URL url = new URL http://www. atguigu.com/");
- public URL(URL context, String spec ))：通过基 URL 和相对 URL 构造 一 个 URL 对象。例如 URL downloadUrl = new url , “download.html");
- public URL(String protocol, String host, String file); 例如 new URL("http","www.atguigu.com ", “download. html");
  public URL(String protocol, String host, int port, String file); 例如 : URL gamelan = new
  URL(http", www.atguigu.com ", 80, download.html");
- URL 类的构造器都声明抛出非运行时异常，必须要对这一异常进行处理，通常是用 try-catch 语句进行捕获。
- 一 个 URL 对象生成后，其属性是不能被改变的，但可以通过它给定的
  方法来获取这些属性：
  - public String getProtocol ( ) 获取该 URL 的协议名
  - public String getHost ( ) 获取 该 URL 的主机名
  - public String getPort ( ) 获取该 URL 的端口号
  - public String getPath ( ) 获取该 URL 的文件路径
  - public String getFile ( ) 获取 该 URL 的文件名
  - public String getQuery ( ) 获取 该 URL 的查询名

**针对 HTTP 协议的 URLConnection**

### 第八章 Java 反射机制

##### 1. Java 反射机制概述

**定义**：在程序执行期借助于 Reflection API 取得任何类的内部信息，并能直接操作任意对象的内部属性和方法。

**反射原理**：加载完类之后，在堆内存的方法区中就产生了一个 Class 类型的对象（一个类只有一个 Class 对象），这个对象就包含了完整的类的结构信息。我们可以通过这个对象看到类的结构。这个对象就像一面镜子，透过这个镜子看到类的结构，所以，我们形象的称之为：反射。  

 **正常方式**： 引入需要的”包类”名称 &rarr; 通过new实例化 &rarr;取得实例化对象

反射方式：  实例化对象 &rarr; getClass()方法 &rarr; 得到完整的“包类”名称 

> 动态语言 VS 静态语言
>
> **动态语言**： 是一类在运行时可以改变其结构的语言：例如新的函数、对象、甚至代码可以被引进，已有的函数可以被删除或是其他结构上的变化。通俗点说就是在运行时代码可以根据某些条件改变自身结构。 主要动态语言：Object-C、C#、JavaScript、PHP、Python、Erlang。 
>
> **静态语言**： 与动态语言相对应的，运行时结构不可变的语言就是静态语言。如Java、C、 C++。 Java不是1言，但 Java 可以称之为“伪动态语言”。即 Java 有一定的动态性，我们可以利用反射机制、字节码操作获得类似动态语言的特性。 
>
> Java的动态性让编程的时候更加灵活！ 

**Java 反射机制提供的功能**

- 在运行时判断任意一个对象所属的类
- 在运行时构造任意一个类的对象
- 在运行时判断任意一个类所具有的成员变量和方法
- 在运行时获取泛型信息
- 在运行时调用任意一个对象的成员变量和 方法
- 在运行时处理注解
- 生成动态代理

**反射相关的 API**

- java.lang.Class 代表一个类
- java.lang.reflect.Method 代表类的方法
- java.lang.reflect.Field 代表类的成员变量
- java.lang.reflect.Constructor 代表类的构造器
- …

> 类本身也是一个对象，它们都是 java.lang.Class 类的实例。

```java
public class Person {
    
    private String name;
    private int age;

    public Person() {
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    private Person(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public void show(){
        System.out.println("你好我是一个好人");
    }

    private String showNation(String nation){
        System.out.println("我的国籍是"+nation);
        return nation;
    }

}
```

使用反射之前：

```java
//1.创建Person类的对象
Person p1 = new Person("Tom", 12);

//2.通过对象，调用其内部的属性、方法
p1.age = 10;
System.out.println(p1.toString());

p1.show();

//在 Person 类外部，不可以通过 Person 类的对象调用其内部私有结构。
//比如：name、showNation()以及私有的构造器

```

使用反射之后：

```java
		Class clazz = Person.class;
        //1.通过反射，创建Person类的对象
        Constructor cons = clazz.getConstructor(String.class,int.class);
        Object obj = cons.newInstance("Tom", 12);
        Person p = (Person) obj;
        System.out.println(p.toString());
        //2.通过反射，调用对象指定的属性、方法
        //调用属性
        Field age = clazz.getDeclaredField("age");
		age.setAccessible(true);
        age.set(p,10);
        System.out.println(p.toString());

        //调用方法
        Method show = clazz.getDeclaredMethod("show");
        show.invoke(p);

        System.out.println("*******************************");

        //通过反射，可以调用Person类的私有结构的。比如：私有的构造器、方法、属性
        //调用私有的构造器
        Constructor cons1 = clazz.getDeclaredConstructor(String.class);
        cons1.setAccessible(true);
        Person p1 = (Person) cons1.newInstance("Jerry");
        System.out.println(p1);

        //调用私有的属性
        Field name = clazz.getDeclaredField("name");
        name.setAccessible(true);
        name.set(p1,"HanMeimei");
        System.out.println(p1);

        //调用私有的方法
        Method showNation = clazz.getDeclaredMethod("showNation", String.class);
        showNation.setAccessible(true);
        String nation = (String) showNation.invoke(p1,"中国");//相当于String nation = p1.showNation("中国")
        System.out.println(nation);

```

疑问1：反射机制与面向对现象的封装性是不是矛盾的？如何看待两个技术？

不矛盾。封装是建议，反射是能不能。

疑问2：通过直接 new 的方式或反射的凡是都可以调用公共的结构，开发中应该用那个？

建议：直接 new 的方式

什么时候会使用反射。根据反射的特征：动态性。编译时无法确定对象的类型，就是用反射。

> 例如在后台编写程序，我们不知道用户会发送过来什么请求。login\regsit 这时我们就无法确定要创建的对象，这时使用反射。

##### 2. 理解 Class 类获取并获取实例

**类的加载过程**

1. 程序经过 javac.exe 命令后，会生成一个或多个字节码文件(.class)。

   使用 java.exe 命令对某个字节码文件进行解释运行。相当于把某个字节码文件加载到内存中了。此过程称为类的加载。加载到内存中的类，称为运行时类，此运行时类就作为 Class 的一个实例。换句话说，Class 的实例就对应着一个运行时类。

2. Class  的实例就是一个运行时类

3. 加载到内存中运行时类，会缓存一定的时间。在此时间之内，我们可以通过不同的方式获取运行时类。

**1. 获取 Class 实例的三种方式(前三种掌握)**

- 调用运行时类的属性：

  前提：已知具体的类，通过类的 class 属性获取。该方法最为安全可靠，程序性能最高。但是把程序给写死了

  `Class claszz1 = Person.class;` 格式：Class 变量名 = 类名.class;

- 通过运行时类的对象，调用 getClass()

  前提：已知某个类的实例，调用该实例的 getClass 方法获取 Class 对象

  `Person p1 = new Person();Class clazz2 = p1.getClass(); `

- 调用 Class 的静态方法：forName

  前提：已知某个类的全类名和类所在的路径，通过 Class 类的静态方法forName()获取，可能抛出ClassNotFoundException 

  `Class clazz3 = Class.forName("com.atguigu.Person");`// forName("包名.类名");

- 使用类的加载器： ClassLoader

  ```java
  ClassLoader classloader = ReflectionTest.class.getClassLoader();//Reflection.class 为当前语句所在的类
  Class clazz4 = ClassLoader.loadClass("包名.类名");
  ```

哪些类型可以有 Class 对象：

1. class： 外部类，成员(成员内部类，静态内部类)，局部内部类，匿名内部类 、
2. interface：接口 
3. []：数组 
4. enum：枚举 
5. annotation：注解@interface 
6. primitive type：基本数据类型 
7. void 

*只要元素的类型和维度一样就是同一个 Class*。举例：

```java
# class 实例可以是那些结构的说明
        Class c1 = Object.class;
        Class c2 = Comparable.class;
        Class c3 = String[].class;
        Class c4 = int[][].class;
        Class c5 = ElementType.class;
        Class c6 = Override.class;
        Class c7 = int.class;
        Class c8 = void.class;
        Class c9 = Class.class;

        int[] a = new int[10];
        int[] b = new int[100];
        Class c10 = a.getClass();
        Class c11 = b.getClass();
        System.out.println(c10 == c11);//只要元素的类型和维度一样就是同一个 Class*
```

**类的加载过程**

当程序主动使用某个类时，如果该类还未被加载到内存中，则系统会通过 如下三个步骤来对该类进行初始化：

① 加载（Load）：将类的 class 文件字节码读入内存，并为之创建一个 java.lang.Class 对象。此过程由类加载器完成

将 class 文件字节码内容加载到内存中，并将这些静态数据转换成方法区的运行时数据结构，然后生成一个代表这个类的 java.lang.Class 对象，作为方法区中类数据的访问 入口（即引用地址）。所有需要访问和使用类数据只能通过这个Class 对象。这个加载的 过程需要类加载器参与。

② 链接（Link）：将类的二进制数据合并到 JRE 中

- 验证：确保加载的类信息符合JVM规范，例如：以cafe开头，没有安全方面的问题
- 准备：正式为类变量（static）分配内存并设置类变量默认初始值的阶段，这些内存都将在方法区中进行分配。
- 解析：虚拟机常量池内的符号引用（常量名）替换为直接引用（地址）的过程。

③ 初始化（Initialize）: JVM 负责对类进行初始化

- 执行类构造器 <clinit>() 方法的过程。类构造器()方法是由编译期自动收集类中 所有类变量的赋值动作和静态代码块中的语句合并产生的。（类构造器是构造类信 息的，不是构造该类对象的构造器）。 
- 当初始化一个类的时候，如果发现其父类还没有进行初始化，则需要先触发其父类 的初始化。 
- 虚拟机会保证一个类的 () 方法在多线程环境中被正确加锁和同步。

```java
public class ClassLoadingTest {
public static void main(String[] args) {
System.out.println(A.m);
}
}
class A {
static {
m = 300;
}
static int m = 100;
}
//第二步：链接结束后m=0
//第三步：初始化后，m的值由<clinit>()方法执行决定
// 这个A的类构造器<clinit>()方法由类变量的赋值和静态代码块中的语句按照顺序合并产生，类似于
// <clinit>(){
// m = 300;
// m = 100;
// }
```

什么时候会发生类初始化？

类的主动引用（一定会发生的类初始化）

- 当虚拟机启动，先初始化 main 方法所在的类 
- new一个类的对象 
- 调用类的静态成员（除了 final 常量）和静态方法
- 使用 java.lang.reflect 包的方法对类进行反射调用
- 当初始化一个类，如果其父类没有被初始化，则先会初始化它的父类

类的被动引用（不会发生类的初始化）

- 当访问一个静态域时，只有真正声明这个域的类才会被初始化
  - 当通过子类引用父类的静态变量，不会导致子类初始化
- 通过数组定义类引用，不会触发此类的初始化
- 引用常量不会触发此类的初始化（常量在链接阶段就存入调用类的常 量池中了）

```java
public class Father {
    static int b = 2;
    static {
        System.out.println("父类被加载");
    }
}

public class A extends Father {
    static {
        System.out.println("子类被加载");
        m = 300;
    }
    static int m = 100;
    static final int M = 10;
}

public class ClassLoadingTest {
public static void main(String[] args) {
// 主动引用：一定会导致A和Father的初始化
// A a = new A();
// System.out.println(A.m);
// Class.forName("com.atguigu.java2.A");
// 被动引用
A[] array = new A[5];//不会导致A和Father的
初始化
// System.out.println(A.b);//只会初始化
Father
// System.out.println(A.M);//不会导致A和
Father的初始化
}
static {
System.out.println("main所在的类");
}
}

```

Class 类的常用方法:

| 方法名                                           | 功能说明                        |
| ------------------------------------------------ | ------------------------------- |
| static Class forName(String name)                | 返回指定类名 name 的 Class 对象 |
| Object newInstance()                             |                                 |
| getName()                                        |                                 |
| Class getSuperClass()                            |                                 |
| Class[] getInterfaces()                          |                                 |
| ClassLoader getClassLoader()                     |                                 |
| Class getSuperclass()                            |                                 |
| Constructor[] getConstructors()                  |                                 |
| Field[] getDeclaredFields()                      |                                 |
| Method getMethod(String name,Class … paramTypes) |                                 |
|                                                  |                                 |

##### 3.类的加载与 ClassLoader 的理解

- 类加载器的作用：将 class 文件字节码内容加载到内存中，并将这些数据转换为**方法区**的运行时数据结构，然后再堆中生成一个代表这个类的 Java.lang.Class 对象，作为方法区中类数据的访问入口
- 类缓存：标准的 JavaSE 类加载器可以按照要求查找类，但某一个类被加载到类加载器中，它将维持加载(缓存)一段时间，不过 JVM 垃圾回收机制可以回收这些 Class 对象。

了解：ClassLoader

类加载器作用是用来把类(class)装载进内存的。JVM 规范定义了如下类型的 类的加载器。

- 引导类加载器：用 C++ 编写的，是 JVM 自带的类加载器，负责 Java 平台核心库，用来装载核心类 库。该加载器无法直接获取
- 扩展类加载器：负责 jre/lib/ext 目录下的 jar 包或 – D java.ext.dirs 指定目录下的 jar 包装入工作库
- 系统类加载器：负责 java –classpath 或 –D  java.class.path 所指的目录下的类与 jar 包装入工 作 ，是最常用的加载器

1. 对于自定义类使用系统类加载器
2. 调用系统类加载器 getParent()，获取扩展类加载器
3. 调用扩展类加载器 getParent()，无法获取引导类加载器
4. 引导类加载器主要负责加载 Java 的核心类库，无法加载自定义类

```java
public class ClassLoaderTest {

    @Test
    public void test1(){
        //对于自定义类，使用系统类加载器进行加载
        ClassLoader classLoader = ClassLoaderTest.class.getClassLoader();
        System.out.println(classLoader);
        //调用系统类加载器的getParent()：获取扩展类加载器
        ClassLoader classLoader1 = classLoader.getParent();
        System.out.println(classLoader1);
        //调用扩展类加载器的getParent()：无法获取引导类加载器
        //引导类加载器主要负责加载java的核心类库，无法加载自定义类的。
        ClassLoader classLoader2 = classLoader1.getParent();
        System.out.println(classLoader2);
                    //测试当前类由哪个类加载器进行加载
            ClassLoader classLoader = Class.forName("com.atguigu.reflection.ClassLoaderTest").getClassLoader();
            System.out.println(classLoader);
            //测试JDK提供的Object类由哪个类加载器加载
            ClassLoader classLoader1 = Class.forName("java.lang.Object").getClassLoader();
            System.out.println(classLoader1);

        ClassLoader classLoader3 = String.class.getClassLoader();
        System.out.println(classLoader3);

    }
    /*
    Properties：用来读取配置文件。

     */
    @Test
    public void test2() throws Exception {

        Properties pros =  new Properties();
        //此时的文件默认在当前的module下。
        //读取配置文件的方式一：
//        FileInputStream fis = new FileInputStream("jdbc.properties");
//        FileInputStream fis = new FileInputStream("src\\jdbc1.properties");
//        pros.load(fis);

        //读取配置文件的方式二：使用 ClassLoader
        //配置文件默认识别为：当前module的src下
        ClassLoader classLoader = ClassLoaderTest.class.getClassLoader();
        InputStream is = classLoader.getResourceAsStream("jdbc1.properties");
        pros.load(is);


        String user = pros.getProperty("user");
        String password = pros.getProperty("password");
        System.out.println("user = " + user + ",password = " + password);

    }

}

```

##### 4. 创建运行时类的对象

有了 Class 对象，能做什么？

创建类的对象：调用 Class 对象的 newInstance() 方法

newInstance()：调用此方法，创建对应的运行时类的对象。内部调用了运行时类的空参的构造器。

 要想此方法正常的创建运行时类的对象，要求：

1\. 运行时类必须有空参的构造器

2\. 空参的构造器的访问权限需要足够。通常，设置为public。

难道没有无参的构造器就不能创建对象了吗？ 

不是！只要在操作的时候明确的调用类中的构造器，并将参数传递进去之后，才可以实例化操作。 

步骤如下： 

① 通过 Class 类的 getDeclaredConstructor(Class … parameterTypes) 取得本类的指定形参类型列表的构造器 

② 向构造器的形参中传递一个对象数组进去，里面包含了构造器中所需的各个参数。 

③ 通过 Constructor 实例化对象。

```java
public class NewInstanceTest {

    @Test
    public void test1() throws IllegalAccessException, InstantiationException {

        Class<Person> clazz = Person.class;
        /*
        newInstance():调用此方法，创建对应的运行时类的对象。内部调用了运行时类的空参的构造器。

        要想此方法正常的创建运行时类的对象，要求：
        1.运行时类必须提供空参的构造器
        2.空参的构造器的访问权限得够。通常，设置为public。


        在 javabean 中要求提供一个 public 的空参构造器。原因：
        1.便于通过反射，创建运行时类的对象
        2.便于子类继承此运行时类时，默认调用super()时，保证父类有此构造器

         */
        Person obj = clazz.newInstance();
        System.out.println(obj);

    }

    //体会反射的动态性
    @Test
    public void test2(){

        for(int i = 0;i < 100;i++){
            int num = new Random().nextInt(3);//0,1,2
            String classPath = "";
            switch(num){
                case 0:
                    classPath = "java.util.Date";
                    break;
                case 1:
                    classPath = "java.lang.Object";
                    break;
                case 2:
                    classPath = "com.atguigu.java.Person";
                    break;
            }

            try {
                Object obj = getInstance(classPath);
                System.out.println(obj);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }



    }

    /*
    创建一个指定类的对象。
    classPath:指定类的全类名
     */
    public Object getInstance(String classPath) throws Exception {
       Class clazz =  Class.forName(classPath);
       return clazz.newInstance();
    }

}
```

注：newInstance() 方法已被废弃使用`clazz.getDeclaredConstractor().newInstance()`方法进行取代。

##### 5. 获取运行时类的完整结构

获取运行时类的属性：

```java
      Class clazz = Person.class;

        //获取属性结构
        //getFields():获取当前运行时类及其父类中声明为public访问权限的属性
        Field[] fields = clazz.getFields();
        for(Field f : fields){
            System.out.println(f);
        }
        System.out.println();

        //getDeclaredFields():获取当前运行时类中声明的所有属性。（不包含父类中声明的属性）
        Field[] declaredFields = clazz.getDeclaredFields();
        for(Field f : declaredFields){
            System.out.println(f);
        }


 		Class clazz = Person.class;
        Field[] declaredFields = clazz.getDeclaredFields();
        for(Field f : declaredFields){
            //1.权限修饰符
            int modifier = f.getModifiers();
            System.out.print(Modifier.toString(modifier) + "\t");

            //2.数据类型
            Class type = f.getType();
            System.out.print(type.getName() + "\t");

            //3.变量名
            String fName = f.getName();
            System.out.print(fName);

            System.out.println();
```

获取方法

- getMethods():获取当前运行时类及其所有父类中声明为public权限的方法
- getDeclaredMethods():获取当前运行时类中声明的所有方法。（不包含父类中声明的方法）
- 获取方法的

```java
  		//权限修饰符  返回值类型  方法名(参数类型1 形参名1,...) throws XxxException{}
		Class clazz = Person.class;
        Method[] declaredMethods = clazz.getDeclaredMethods();
        for(Method m : declaredMethods){
            //1.获取方法声明的注解
            Annotation[] annos = m.getAnnotations();
            for(Annotation a : annos){
                System.out.println(a);
            }

            //2.权限修饰符
            System.out.print(Modifier.toString(m.getModifiers()) + "\t");

            //3.返回值类型
            System.out.print(m.getReturnType().getName() + "\t");

            //4.方法名
            System.out.print(m.getName());
            System.out.print("(");
            //5.形参列表
            Class[] parameterTypes = m.getParameterTypes();
            if(!(parameterTypes == null && parameterTypes.length == 0)){
                for(int i = 0;i < parameterTypes.length;i++){

                    if(i == parameterTypes.length - 1){
                        System.out.print(parameterTypes[i].getName() + " args_" + i);
                        break;
                    }

                    System.out.print(parameterTypes[i].getName() + " args_" + i + ",");
                }
            }

            System.out.print(")");

            //6.抛出的异常
            Class[] exceptionTypes = m.getExceptionTypes();
            if(exceptionTypes.length > 0){
                System.out.print("throws ");
                for(int i = 0;i < exceptionTypes.length;i++){
                    if(i == exceptionTypes.length - 1){
                        System.out.print(exceptionTypes[i].getName());
                        break;
                    }

                    System.out.print(exceptionTypes[i].getName() + ",");
                }
            }

            System.out.println();
        }
```

**获取构造器**

- getConstructors():获取当前运行时类中声明为public的构造器
- getDeclaredConstructors():获取当前运行时类中声明的所有的构造器

**获取运行时类的父类及其泛型**

- getSuperClass(): 获取运行时类的父类

- getGenericSuperclass()； 获取运行时类的带泛型的父类

- 获取运行时类的带泛型的父类的泛型

  ```java
  		 Class clazz = Person.class;
          Type genericSuperclass = clazz.getGenericSuperclass();
          ParameterizedType paramType = (ParameterizedType) genericSuperclass;
          //获取泛型类型
          Type[] actualTypeArguments = paramType.getActualTypeArguments();
          System.out.println(((Class)actualTypeArguments[0]).getName());
  ```

  

- getInterfaces();  获取运行时类实现的接口

- getPackage(); 获取运行时类所在的包

- getAnnotations();  获取运行时类声明的注解

##### 6. 调用运行时类的指定结构

获取运行时类指定的属性

- getField() 不常用

  ```java
  Class clazz = Person.class;
  
          //创建运行时类的对象
          Person p = (Person) clazz.newInstance();
  
  
          //获取指定的属性：要求运行时类中属性声明为public
          //通常不采用此方法
          Field id = clazz.getField("id");
  
          /*
          设置当前属性的值
  
          set():参数1：指明设置哪个对象的属性   参数2：将此属性值设置为多少
           */
  
          id.set(p,1001);
  
          /*
          获取当前属性的值
          get():参数1：获取哪个对象的当前属性值
           */
          int pId = (int) id.get(p);
          System.out.println(pId);
  ```

- getDeclaredField(String fieldName):获取运行时类中指定变量名的属性 常用

  ```java
  		Class clazz = Person.class;
  
          //创建运行时类的对象
          Person p = (Person) clazz.newInstance();
  
          //1. getDeclaredField(String fieldName):获取运行时类中指定变量名的属性
          Field name = clazz.getDeclaredField("name");
  
          //2.保证当前属性是可访问的
          name.setAccessible(true);
          //3.获取、设置指定对象的此属性值
          name.set(p,"Tom");
  
          System.out.println(name.get(p))
  ```

调用运行时类的方法：

- getField(); 略

- getDeclaredMethod(); 常用

  ```java
          Class clazz = Person.class;
  
          //创建运行时类的对象
          Person p = (Person) clazz.newInstance();
  
          /*
          1.获取指定的某个方法
          getDeclaredMethod():参数1 ：指明获取的方法的名称  参数2：指明获取的方法的形参列表
           */
          Method show = clazz.getDeclaredMethod("show", String.class);
          //2.保证当前方法是可访问的
          show.setAccessible(true);
  
          /*
          3. 调用方法的invoke():参数1：方法的调用者  参数2：给方法形参赋值的实参
          invoke()的返回值即为对应类中调用的方法的返回值。
           */
          Object returnValue = show.invoke(p,"CHN"); //String nation = p.show("CHN");
          System.out.println(returnValue);
  ```

- 调用静态方法

  ```java
   Method showDesc = clazz.getDeclaredMethod("showDesc");
          showDesc.setAccessible(true);
          //如果调用的运行时类中的方法没有返回值，则此invoke()返回null
  //        Object returnVal = showDesc.invoke(null);
          Object returnVal = showDesc.invoke(Person.class);
          System.out.println(returnVal);//null
  ```

用运行时类中指定的构造器：

```java
 Class clazz = Person.class;

        //private Person(String name)
        /*
        1.获取指定的构造器
        getDeclaredConstructor():参数：指明构造器的参数列表
         */

        Constructor constructor = clazz.getDeclaredConstructor(String.class);

        //2.保证此构造器是可访问的
        constructor.setAccessible(true);

        //3.调用此构造器创建运行时类的对象
        Person per = (Person) constructor.newInstance("Tom");
        System.out.println(per);
注：直接通过类来调用 newInstance() 方法已过时，使用 clazz.getConstructor().newInstance() 或 clazz.getDeclaredConstructor().newInstance)() 来调用
```

  ##### 7. 反射的应用 动态代理

```java
/**
 *
 * 动态代理的举例
 *
 * @author shkstart
 * @create 2019 上午 10:18
 */

interface Human{

    String getBelief();

    void eat(String food);

}
//被代理类
class SuperMan implements Human{


    @Override
    public String getBelief() {
        return "I believe I can fly!";
    }

    @Override
    public void eat(String food) {
        System.out.println("我喜欢吃" + food);
    }
}

class HumanUtil{

    public void method1(){
        System.out.println("====================通用方法一====================");

    }

    public void method2(){
        System.out.println("====================通用方法二====================");
    }

}

/*
要想实现动态代理，需要解决的问题？
问题一：如何根据加载到内存中的被代理类，动态的创建一个代理类及其对象。
问题二：当通过代理类的对象调用方法a时，如何动态的去调用被代理类中的同名方法a。


 */

class ProxyFactory{
    //调用此方法，返回一个代理类的对象。解决问题一
    public static Object getProxyInstance(Object obj){//obj:被代理类的对象
        MyInvocationHandler handler = new MyInvocationHandler();

        handler.bind(obj);

        return Proxy.newProxyInstance(obj.getClass().getClassLoader(),obj.getClass().getInterfaces(),handler);
    }

}

class MyInvocationHandler implements InvocationHandler{

    private Object obj;//需要使用被代理类的对象进行赋值

    public void bind(Object obj){
        this.obj = obj;
    }

    //当我们通过代理类的对象，调用方法a时，就会自动的调用如下的方法：invoke()
    //将被代理类要执行的方法a的功能就声明在invoke()中
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        HumanUtil util = new HumanUtil();
        util.method1();

        //method:即为代理类对象调用的方法，此方法也就作为了被代理类对象要调用的方法
        //obj:被代理类的对象
        Object returnValue = method.invoke(obj,args);

        util.method2();

        //上述方法的返回值就作为当前类中的invoke()的返回值。
        return returnValue;

    }
}

public class ProxyTest {

    public static void main(String[] args) {
        SuperMan superMan = new SuperMan();
        //proxyInstance:代理类的对象
        Human proxyInstance = (Human) ProxyFactory.getProxyInstance(superMan);
        //当通过代理类对象调用方法时，会自动的调用被代理类中同名的方法
        String belief = proxyInstance.getBelief();
        System.out.println(belief);
        proxyInstance.eat("四川麻辣烫");

        System.out.println("*****************************");

        NikeClothFactory nikeClothFactory = new NikeClothFactory();

        ClothFactory proxyClothFactory = (ClothFactory) ProxyFactory.getProxyInstance(nikeClothFactory);

        proxyClothFactory.produceCloth();

    }
}

```



1. 写出获取Class实例的三种常见方式

2. 谈谈你对Class类的理解

3. 创建 Class 对应运行时类的对象的通用方法，代码实现。以及这样操作，需要对应的运行时类构造器方面满足的要求。

4. 在工程或module的src下有名为”jdbc.properties”的配置文件，文件内容为：name=Tom。如何在程序中通过代码获取 Tom 这个变量值。代码实现

```java
Properties pros = new Properties();
FileIntputStream fis = new FileIntputStream();
pros.load(fis);
String name = pros.getProperty("name");
```



5. 如何调用方法show() 

```java
//类声明如下：

package com.atguigu.java;

class User{

	    public void show(){

       System.out.println(“我是一个中国人！”); 

} }


 Class clazz = Class.forName("User");
            Method show = clazz.getDeclaredMethod("show");
            show.setAccessible(true);
            User user = (User) clazz.getDeclaredConstructor().newInstance();
            show.invoke(user);
```

### 第九章 Java 8 的其它新特性

##### 1. Lambda 表达式

> lambda 表达式是函数式接口的实现

1. 1.举例： `(o1,o2) -> Integer.compare(o1,o2);`

2. 格式：

   - `->` : lamdba 操作符 或 箭头操作符
   - `->` 左边：lambda 形参列表 (其实就是接口中抽象方法的形参列表)
   - `->` 右边：lambda 体 （重写方法的方法体）

3. lambda 表达式的使用：(分为 6 种情况介绍)

   总结：

   `->` 左边：lambda 形参列表的参数类型可以省略(类型推断)；如果 lambda 形参列表只有一个参数，其 `()` 也可以省略

    `-> `右边：lambda体 应该使用一对 `{}` 包裹；如果lambda体只有一条执行语句（可能是 return语句），省略这一对 {} 和 return关键字

4. Lambda表达式的本质：作为函数式接口的实例

5. . 如果一个接口中，只声明了一个抽象方法，则此接口就称为函数式接口。我们可以在一个接口上使用 @FunctionalInterface 注解，

   *   这样做可以检查它是否是一个函数式接口

6. 所以以前用匿名实现类表示的现在都可以用Lambda表达式来写。

注意对接口有要求：接口中只能由一个抽象方法

**Lambda 若只需要一个参数时，参数的小括号可以省略**

**无参，无返回值**

```java
public class LambdaTest1 {
    //语法格式一：无参，无返回值
    @Test
    public void test1(){
        Runnable r1 = new Runnable() {
            @Override
            public void run() {
                System.out.println("我爱北京天安门");
            }
        };

        r1.run();

        System.out.println("***********************");

        Runnable r2 = () -> {
            System.out.println("我爱北京故宫");
        };

        r2.run();
    }
    //语法格式二：Lambda 需要一个参数，但是没有返回值。
    @Test
    public void test2(){

        Consumer<String> con = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        con.accept("谎言和誓言的区别是什么？");

        System.out.println("*******************");

        Consumer<String> con1 = (String s) -> {
            System.out.println(s);
        };
        con1.accept("一个是听得人当真了，一个是说的人当真了");

    }

    //语法格式三：数据类型可以省略，因为可由编译器推断得出，称为“类型推断”
    //我们是对接口中的抽象方法进行重写，这样方法的参数类型也再接口中就确定了。
    @Test
    public void test3(){

        Consumer<String> con1 = (String s) -> {
            System.out.println(s);
        };
        con1.accept("一个是听得人当真了，一个是说的人当真了");

        System.out.println("*******************");

        Consumer<String> con2 = (s) -> {
            System.out.println(s);
        };
        con2.accept("一个是听得人当真了，一个是说的人当真了");

    }

    @Test
    public void test4(){

        ArrayList<String> list = new ArrayList<>();//类型推断

        int[] arr = {1,2,3};//类型推断

    }

    //语法格式四：Lambda 若只需要一个参数时，参数的小括号可以省略
    @Test
    public void test5(){
        Consumer<String> con1 = (s) -> {
            System.out.println(s);
        };
        con1.accept("一个是听得人当真了，一个是说的人当真了");

        System.out.println("*******************");

        Consumer<String> con2 = s -> {
            System.out.println(s);
        };
        con2.accept("一个是听得人当真了，一个是说的人当真了");


    }

    //语法格式五：Lambda 需要两个或以上的参数，多条执行语句，并且可以有返回值
    @Test
    public void test6(){

        Comparator<Integer> com1 = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                System.out.println(o1);
                System.out.println(o2);
                return o1.compareTo(o2);
            }
        };

        System.out.println(com1.compare(12,21));

        System.out.println("*****************************");
        Comparator<Integer> com2 = (o1,o2) -> {
            System.out.println(o1);
            System.out.println(o2);
            return o1.compareTo(o2);
        };

        System.out.println(com2.compare(12,6));


    }

    //语法格式六：当 Lambda 体只有一条语句时，return 与大括号若有，都可以省略
    @Test
    public void test7(){

        Comparator<Integer> com1 = (o1,o2) -> {
            return o1.compareTo(o2);
        };

        System.out.println(com1.compare(12,6));

        System.out.println("*****************************");

        Comparator<Integer> com2 = (o1,o2) -> o1.compareTo(o2);

        System.out.println(com2.compare(12,21));

    }

    @Test
    public void test8(){
        Consumer<String> con1 = s -> {
            System.out.println(s);
        };
        con1.accept("一个是听得人当真了，一个是说的人当真了");

        System.out.println("*****************************");

        Consumer<String> con2 = s -> System.out.println(s);

        con2.accept("一个是听得人当真了，一个是说的人当真了");

    }

}

```

##### 2. Java 中的四大函数式接口

```java
/**
 * java内置的4大核心函数式接口
 *
 * 消费型接口 Consumer<T>     void accept(T t)
 * 供给型接口 Supplier<T>     T get()
 * 函数型接口 Function<T,R>   R apply(T t)
 * 断定型接口 Predicate<T>    boolean test(T t)
 *
 *
 * @author shkstart
 * @create 2019 下午 2:29
 */
public class LambdaTest2 {
	//原来的写法
    @Test
    public void test1(){

        happyTime(500, new Consumer<Double>() {
            @Override
            public void accept(Double aDouble) {
                System.out.println("学习太累了，去天上人间买了瓶矿泉水，价格为：" + aDouble);
            }
        });

        System.out.println("********************");
		//使用 lambda 的写法
        happyTime(400,money -> System.out.println("学习太累了，去天上人间喝了口水，价格为：" + money));//money 的参数类型在happyTime 中都已经定义过了，无需明示，而后面这条语句，则为 Consumer 匿名实现类的的 accept() 的方法体
    }

    public void happyTime(double money, Consumer<Double> con){
       
        con.accept(money);
    }


    @Test
    public void test2(){
        List<String> list = Arrays.asList("北京","南京","天津","东京","西京","普京");

        List<String> filterStrs = filterString(list, new Predicate<String>() {
            @Override
            public boolean test(String s) {
                return s.contains("京");
            }
        });

        System.out.println(filterStrs);


        List<String> filterStrs1 = filterString(list,s -> s.contains("京"));
        System.out.println(filterStrs1);
    }

    //根据给定的规则，过滤集合中的字符串。此规则由Predicate的方法决定
    public List<String> filterString(List<String> list, Predicate<String> pre){

        ArrayList<String> filterList = new ArrayList<>();

        for(String s : list){
            if(pre.test(s)){
                filterList.add(s);
            }
        }

        return filterList;

    }

}
```

![Snipaste_2020-05-15_22-34-11](C:/doc/Markdown文档/Java/Snipaste_2020-05-15_22-34-11.png)

##### 3. 方法引用与构造器引用

- 当要传递给 lambda 体的操作，已经有实现的方法了，那么使用方法引用
- 方法引用可以看作是 Lambda 表达式深层次的表达。换句话说 lambda 表达式，也就是函数式接口的一个实例。通过方法的方法名来指向一个方法，可以认为是 Lamdba 表达式的一个语法堂。
- 要求：实现接口的抽象方法的参数列表和返回值类型，必须与方法引用的 方法的参数列表和返回值类型保持一致！
- 格式：使用操作符 “::” 将类(或对象) 与 方法名分隔开来。 
- 如下三种主要使用情况： 
  - 对象::实例方法名 
  - 类::静态方法名 
  - 类::实例方法名

```java
public class MethodRefTest {

	// 情况一：对象 :: 实例方法
	//Consumer中的void accept(T t)
	//PrintStream中的void println(T t)
	@Test
	public void test1() {
		Consumer<String> con1 = str -> System.out.println(str);
		con1.accept("北京");

		System.out.println("*******************");
		PrintStream ps = System.out;
		Consumer<String> con2 = ps::println;
		con2.accept("beijing");
	}
	
	//Supplier中的T get()
	//Employee中的String getName()
	@Test
	public void test2() {
		Employee emp = new Employee(1001,"Tom",23,5600);

		Supplier<String> sup1 = () -> emp.getName();
		System.out.println(sup1.get());

		System.out.println("*******************");
		Supplier<String> sup2 = emp::getName;
		System.out.println(sup2.get());

	}

	// 情况二：类 :: 静态方法
	//Comparator中的int compare(T t1,T t2)
	//Integer中的int compare(T t1,T t2)
	@Test
	public void test3() {
		Comparator<Integer> com1 = (t1,t2) -> Integer.compare(t1,t2);
		System.out.println(com1.compare(12,21));

		System.out.println("*******************");

		Comparator<Integer> com2 = Integer::compare;
		System.out.println(com2.compare(12,3));

	}
	
	//Function中的R apply(T t)
	//Math中的Long round(Double d)
	@Test
	public void test4() {
		Function<Double,Long> func = new Function<Double, Long>() {
			@Override
			public Long apply(Double d) {
				return Math.round(d);
			}
		};

		System.out.println("*******************");

		Function<Double,Long> func1 = d -> Math.round(d);
		System.out.println(func1.apply(12.3));

		System.out.println("*******************");

		Function<Double,Long> func2 = Math::round;
		System.out.println(func2.apply(12.6));
	}

	// 情况三：类 :: 实例方法  (有难度)
	// Comparator中的int comapre(T t1,T t2)
	// String中的int t1.compareTo(t2)
	@Test
	public void test5() {
		Comparator<String> com1 = (s1,s2) -> s1.compareTo(s2);
		System.out.println(com1.compare("abc","abd"));

		System.out.println("*******************");

		Comparator<String> com2 = String :: compareTo;
		System.out.println(com2.compare("abd","abm"));
	}

	//BiPredicate中的boolean test(T t1, T t2);
	//String中的boolean t1.equals(t2)
	@Test
	public void test6() {
		BiPredicate<String,String> pre1 = (s1,s2) -> s1.equals(s2);
		System.out.println(pre1.test("abc","abc"));

		System.out.println("*******************");
		BiPredicate<String,String> pre2 = String :: equals;
		System.out.println(pre2.test("abc","abd"));
	}
	
	// Function中的R apply(T t)
	// Employee中的String getName();
	@Test
	public void test7() {
		Employee employee = new Employee(1001, "Jerry", 23, 6000);


		Function<Employee,String> func1 = e -> e.getName();
		System.out.println(func1.apply(employee));

		System.out.println("*******************");


		Function<Employee,String> func2 = Employee::getName;
		System.out.println(func2.apply(employee));

	}

}

```

##### 4. 构造器引用与数组引用

```java
/**
 * 一、构造器引用
 *      和方法引用类似，函数式接口的抽象方法的形参列表和构造器的形参列表一致。
 *      抽象方法的返回值类型即为构造器所属的类的类型
 *
 * 二、数组引用
 *     大家可以把数组看做是一个特殊的类，则写法与构造器引用一致。
 *
 * Created by shkstart
 */
public class ConstructorRefTest {
	//构造器引用
    //Supplier中的T get()
    //Employee的空参构造器：Employee()
    @Test
    public void test1(){

        Supplier<Employee> sup = new Supplier<Employee>() {
            @Override
            public Employee get() {
                return new Employee();
            }
        };
        System.out.println("*******************");

        Supplier<Employee>  sup1 = () -> new Employee();
        System.out.println(sup1.get());

        System.out.println("*******************");

        Supplier<Employee>  sup2 = Employee :: new;
        System.out.println(sup2.get());
    }

	//Function中的R apply(T t)
    @Test
    public void test2(){
        Function<Integer,Employee> func1 = id -> new Employee(id);
        Employee employee = func1.apply(1001);
        System.out.println(employee);

        System.out.println("*******************");

        Function<Integer,Employee> func2 = Employee :: new;
        Employee employee1 = func2.apply(1002);
        System.out.println(employee1);

    }

	//BiFunction中的R apply(T t,U u)
    @Test
    public void test3(){
        BiFunction<Integer,String,Employee> func1 = (id,name) -> new Employee(id,name);
        System.out.println(func1.apply(1001,"Tom"));

        System.out.println("*******************");

        BiFunction<Integer,String,Employee> func2 = Employee :: new;
        System.out.println(func2.apply(1002,"Tom"));

    }

	//数组引用
    //Function中的R apply(T t)
    @Test
    public void test4(){
        Function<Integer,String[]> func1 = length -> new String[length];
        String[] arr1 = func1.apply(5);
        System.out.println(Arrays.toString(arr1));

        System.out.println("*******************");

        Function<Integer,String[]> func2 = String[] :: new;
        String[] arr2 = func2.apply(10);
        System.out.println(Arrays.toString(arr2));

    }
}
```

##### 5. 强大的 Stream API

- Stream API (java.util.stream) 真正的把函数式接口编程风格引入到 java 中。
- 它可以指定你希望对数据库进行的操作，可以执行非常复杂的查找、过滤、映射数据等操作，使用 Stream API 可以对集合中的数据进行操作，就类似于使用 SQL 执行的数据库查询。

为什么要使用 Stream API?

- 实际开发中，项目中多数数据源源自于 Mysql,Oracle。但现在数据源更多了，有 MongDB，Radis 等，而这些 NoSQL 的数据就需要 Java 层面处理
- Stream 和 Collection 集合的区别： *Collection 是一种静态的内存数据结构，而 Stream 是有关计算的。前者主要面向的是内存，存储在内存中，后者主要是面向 CPU，通过 CPU 进行计算。“集合讲存储数据的，Stream 是做计算的。”*

Steam 是什么？

是数据渠道，用于操作数据源（集合、数组等）所产生当元素序列。

注意：

1. Stream 不会存储元素
2. Stream 不会改变源对象，相反，他们会返回一个持有结果的 Stream
3. Stream 操作是延迟执行的，这意味者他们会等到需要执行时候执行。

Stream 操作的三个步骤：

1. 创建 Stream 的实例

   一个数据源（集合，数组）,获取一个流 

2. 中间操作

   一系列的中间操作（过滤、映射……），

3. 终止操作（终端操作）

4. 说明：

   - 一个中间操作链，对数据源的数据进行处理

   - 一旦执行终止操作，就执行中间操作链，并产生结果，之后不会再被使用。

```java
public class StreamAPITest {

    //创建 Stream方式一：通过集合
    @Test
    public void test1(){
        List<Employee> employees = EmployeeData.getEmployees();

//        default Stream<E> stream() : 返回一个顺序流
        Stream<Employee> stream = employees.stream();

//        default Stream<E> parallelStream() : 返回一个并行流
        Stream<Employee> parallelStream = employees.parallelStream();
		Employee e1 = new Employee(1001,"Tom");
        Employee e2 = new Employee(1002,"Jerry");
        Employee[] arr1 = new Employee[]{e1,e2};
        Stream<Employee> stream1 = Arrays.stream(arr1);
    }

    //创建 Stream方式二：通过数组
    @Test
    public void test2(){
        int[] arr = new int[]{1,2,3,4,5,6};
        //调用Arrays类的static <T> Stream<T> stream(T[] array): 返回一个流
        IntStream stream = Arrays.stream(arr);

    }
    //创建 Stream方式三：通过Stream的of()
    @Test
    public void test3(){

        Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5, 6);

    }

    //创建 Stream方式四：创建无限流
    @Test
    public void test4(){

//      迭代
//      public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f)
        //遍历前10个偶数
        Stream.iterate(0, t -> t + 2).limit(10).forEach(System.out::println);


//      生成
//      public static<T> Stream<T> generate(Supplier<T> s)
        Stream.generate(Math::random).limit(10).forEach(System.out::println);

    }

}
```

中间操作：筛选与切片

```java
public class StreamAPITest1 {

    //1-筛选与切片
    @Test
    public void test1(){
        List<Employee> list = EmployeeData.getEmployees();
//        filter(Predicate p)——接收 Lambda ， 从流中排除某些元素。
        Stream<Employee> stream = list.stream();
        //练习：查询员工表中薪资大于7000的员工信息
        stream.filter(e -> e.getSalary() > 7000).forEach(System.out::println);

        System.out.println();
//        limit(n)——截断流，使其元素不超过给定数量。
        list.stream().limit(3).forEach(System.out::println);
        System.out.println();

//        skip(n) —— 跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流。与 limit(n) 互补
        list.stream().skip(3).forEach(System.out::println);

        System.out.println();
//        distinct()——筛选，通过流所生成元素的 hashCode() 和 equals() 去除重复元素

        list.add(new Employee(1010,"刘强东",40,8000));
        list.add(new Employee(1010,"刘强东",41,8000));
        list.add(new Employee(1010,"刘强东",40,8000));
        list.add(new Employee(1010,"刘强东",40,8000));
        list.add(new Employee(1010,"刘强东",40,8000));

//        System.out.println(list);

        list.stream().distinct().forEach(System.out::println);
    }

    //映射
    @Test
    public void test2(){
//        map(Function f)——接收一个函数作为参数，将元素转换成其他形式或提取信息，该函数会被应用到每个元素上，并将其映射成一个新的元素。
        List<String> list = Arrays.asList("aa", "bb", "cc", "dd");
        list.stream().map(str -> str.toUpperCase()).forEach(System.out::println);

//        练习1：获取员工姓名长度大于3的员工的姓名。
        List<Employee> employees = EmployeeData.getEmployees();
        Stream<String> namesStream = employees.stream().map(Employee::getName);
        namesStream.filter(name -> name.length() > 3).forEach(System.out::println);
        System.out.println();
        //练习2：
        Stream<Stream<Character>> streamStream = list.stream().map(StreamAPITest1::fromStringToStream);
        streamStream.forEach(s ->{
            s.forEach(System.out::println);
        });
        System.out.println();
//        flatMap(Function f)——接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流。
        Stream<Character> characterStream = list.stream().flatMap(StreamAPITest1::fromStringToStream);
        characterStream.forEach(System.out::println);

    }

    //将字符串中的多个字符构成的集合转换为对应的Stream的实例
    public static Stream<Character> fromStringToStream(String str){//aa
        ArrayList<Character> list = new ArrayList<>();
        for(Character c : str.toCharArray()){
            list.add(c);
        }
       return list.stream();

    }



    @Test
    public void test3(){
        ArrayList list1 = new ArrayList();
        list1.add(1);
        list1.add(2);
        list1.add(3);

        ArrayList list2 = new ArrayList();
        list2.add(4);
        list2.add(5);
        list2.add(6);

//        list1.add(list2);
        list1.addAll(list2);
        System.out.println(list1);

    }

    //3-排序
    @Test
    public void test4(){
//        sorted()——自然排序
        List<Integer> list = Arrays.asList(12, 43, 65, 34, 87, 0, -98, 7);
        list.stream().sorted().forEach(System.out::println);
        //抛异常，原因:Employee没有实现Comparable接口
//        List<Employee> employees = EmployeeData.getEmployees();
//        employees.stream().sorted().forEach(System.out::println);


//        sorted(Comparator com)——定制排序

        List<Employee> employees = EmployeeData.getEmployees();
        employees.stream().sorted( (e1,e2) -> {

           int ageValue = Integer.compare(e1.getAge(),e2.getAge());
           if(ageValue != 0){
               return ageValue;
           }else{
               return -Double.compare(e1.getSalary(),e2.getSalary());
           }

        }).forEach(System.out::println);
    }

}
```

**匹配与查找**

```java
public class StreamAPITest2 {

    //1-匹配与查找
    @Test
    public void test1(){
        List<Employee> employees = EmployeeData.getEmployees();

//        allMatch(Predicate p)——检查是否匹配所有元素。
//          练习：是否所有的员工的年龄都大于18
        boolean allMatch = employees.stream().allMatch(e -> e.getAge() > 18);
        System.out.println(allMatch);

//        anyMatch(Predicate p)——检查是否至少匹配一个元素。
//         练习：是否存在员工的工资大于 10000
        boolean anyMatch = employees.stream().anyMatch(e -> e.getSalary() > 10000);
        System.out.println(anyMatch);

//        noneMatch(Predicate p)——检查是否没有匹配的元素。
//          练习：是否存在员工姓“雷”
        boolean noneMatch = employees.stream().noneMatch(e -> e.getName().startsWith("雷"));
        System.out.println(noneMatch);
//        findFirst——返回第一个元素
        Optional<Employee> employee = employees.stream().findFirst();
        System.out.println(employee);
//        findAny——返回当前流中的任意元素
        Optional<Employee> employee1 = employees.parallelStream().findAny();
        System.out.println(employee1);

    }

    @Test
    public void test2(){
        List<Employee> employees = EmployeeData.getEmployees();
        // count——返回流中元素的总个数
        long count = employees.stream().filter(e -> e.getSalary() > 5000).count();
        System.out.println(count);
//        max(Comparator c)——返回流中最大值
//        练习：返回最高的工资：
        Stream<Double> salaryStream = employees.stream().map(e -> e.getSalary());
        Optional<Double> maxSalary = salaryStream.max(Double::compare);
        System.out.println(maxSalary);
//        min(Comparator c)——返回流中最小值
//        练习：返回最低工资的员工
        Optional<Employee> employee = employees.stream().min((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary()));
        System.out.println(employee);
        System.out.println();
//        forEach(Consumer c)——内部迭代
        employees.stream().forEach(System.out::println);

        //使用集合的遍历操作
        employees.forEach(System.out::println);
    }

    //2-归约
    @Test
    public void test3(){
//        reduce(T identity, BinaryOperator)——可以将流中元素反复结合起来，得到一个值。返回 T
//        练习1：计算1-10的自然数的和
        List<Integer> list = Arrays.asList(1,2,3,4,5,6,7,8,9,10);
        Integer sum = list.stream().reduce(0, Integer::sum);
        System.out.println(sum);


//        reduce(BinaryOperator) ——可以将流中元素反复结合起来，得到一个值。返回 Optional<T>
//        练习2：计算公司所有员工工资的总和
        List<Employee> employees = EmployeeData.getEmployees();
        Stream<Double> salaryStream = employees.stream().map(Employee::getSalary);
//        Optional<Double> sumMoney = salaryStream.reduce(Double::sum);
        Optional<Double> sumMoney = salaryStream.reduce((d1,d2) -> d1 + d2);
        System.out.println(sumMoney.get());

    }

    //3-收集
    @Test
    public void test4(){
//        collect(Collector c)——将流转换为其他形式。接收一个 Collector接口的实现，用于给Stream中元素做汇总的方法
//        练习1：查找工资大于6000的员工，结果返回为一个List或Set

        List<Employee> employees = EmployeeData.getEmployees();
        List<Employee> employeeList = employees.stream().filter(e -> e.getSalary() > 6000).collect(Collectors.toList());

        employeeList.forEach(System.out::println);
        System.out.println();
        Set<Employee> employeeSet = employees.stream().filter(e -> e.getSalary() > 6000).collect(Collectors.toSet());

        employeeSet.forEach(System.out::println);

    }
}
```

##### 6. Optional 类

> Optional <T> 类 Java.util.Optional 是一个容器类，它保存类型 T 的值，如果这个值存在。或着仅仅保存 null 表示这个值不存在，原来用 null 表示一个值不存在，现在 Optional  类可以更好的表达这个概念，并且可以避免空指针异常。

```java
/**
 * Optional类：为了在程序中避免出现空指针异常而创建的。
 *
 * 常用的方法：ofNullable(T t)
 *            orElse(T t)
 *
 * @author shkstart
 * @create 2019 下午 7:24
 */
public class OptionalTest {

/*
Optional.of(T t) : 创建一个 Optional 实例，t必须非空；
Optional.empty() : 创建一个空的 Optional 实例
Optional.ofNullable(T t)：t可以为null

 */
    @Test
    public void test1(){
        Girl girl = new Girl();
//        girl = null;
        //of(T t):保证t是非空的
        Optional<Girl> optionalGirl = Optional.of(girl);

    }

    @Test
    public void test2(){
        Girl girl = new Girl();
//        girl = null;
        //ofNullable(T t)：t可以为null
        Optional<Girl> optionalGirl = Optional.ofNullable(girl);
        System.out.println(optionalGirl);
        //orElse(T t1):如果单前的Optional内部封装的t是非空的，则返回内部的t.
        //如果内部的t是空的，则返回orElse()方法中的参数t1.
        Girl girl1 = optionalGirl.orElse(new Girl("赵丽颖"));
        System.out.println(girl1);

    }


    public String getGirlName(Boy boy){
        return boy.getGirl().getName();
    }

    @Test
    public void test3(){
        Boy boy = new Boy();
        boy = null;
        String girlName = getGirlName(boy);
        System.out.println(girlName);

    }
    //优化以后的getGirlName():
    public String getGirlName1(Boy boy){
        if(boy != null){
            Girl girl = boy.getGirl();
            if(girl != null){
                return girl.getName();
            }
        }

        return null;

    }

    @Test
    public void test4(){
        Boy boy = new Boy();
        boy = null;
        String girlName = getGirlName1(boy);
        System.out.println(girlName);

    }

    //使用Optional类的getGirlName():
    public String getGirlName2(Boy boy){

        Optional<Boy> boyOptional = Optional.ofNullable(boy);
        //此时的boy1一定非空
        Boy boy1 = boyOptional.orElse(new Boy(new Girl("迪丽热巴")));

        Girl girl = boy1.getGirl();

        Optional<Girl> girlOptional = Optional.ofNullable(girl);
        //girl1一定非空
        Girl girl1 = girlOptional.orElse(new Girl("古力娜扎"));

        return girl1.getName();
    }

    @Test
    public void test5(){
        Boy boy = null;
        boy = new Boy();
        boy = new Boy(new Girl("苍老师"));
        String girlName = getGirlName2(boy);
        System.out.println(girlName);

    }



}

```

### 第十章 Java 9&10＆11 新特性

PPT 中写的很详细，感觉不需要再记笔记了

##### 1. Java9

主要：模块化系统和 shell 命令

一. 目录变更：略

二. 模块化系统：Jigsaw --> Modularity

通过模块管理各个 package,

模块：就是在 package 外再包裹一层，不声明默认就是隐藏。也就是说用模块管理各个 package，通过声明某个 package 暴露。

模块由通常的类和模块声明文件 moudle-info.java。该文件位于 Java 代码块结构的顶层(src 包中)。该模块描述符明确地定义了我们的模块需要什么依赖关系(package)， 以及哪些模块被外部使用。在exports子句中未提及的所有包默认情况下将封装在模块中，不能在外部使用。

例子：

要想在 java9demo 模块中调用 java9test 模块(moudle)下包中的结构，需要在java9test 的 module-info.java 中声明：

```java
module java9test {
//package we export
exports com.atguigui.bean;
}
```

对应在java 9demo 模块的src 下创建module-info.java文件：

```java
module java9demo {
	requires java9test;
}
```

requires：指明对其它模块的依赖。

三. REPL工具：Jshell 命令

REPL - Read Evalute Print Loop。在命令行中进行就可以进行编程

以下操作全部是在命令行窗口中进行：

- 调出 jshell 命令：`jshell`
- 获取帮助：`/help intro`
- 默认已经导入所有的包
- Tab键自动补全
- 列出当前 session(会话) 中所有有效代码：`/list`
- 查看当前 session 下所有的创建过的变量：`/vare`
- 查看当前 session 中所有创建过的方法：`/methods`
- 使用外部代码编辑器来编辑 Java 方法：`/edit`
- 从文件中加载语句：`/open 文件路径`
- **隐藏异常** , 在 jshell 中不会报异常
- 退出 jshell：`/exit`

我们还可以重新定义相同方法名和参 数列表的方法，即为对现有方法的修改（或 覆盖）。

四. 私有接口方法

例子

```java
interface MyInterface {
	void normalInterfaceMethod();
    default void methodDefault1() {
    init();
    }
    public default void methodDefault2() {
    init();
    }
	// This method is not part of the public API exposed by MyInterface
    private void init() {
        System.out.println("默认方法中的通用操作");
    }
}


class MyInterfaceImpl implements MyInterface {
@Override
public void normalInterfaceMethod() {
System.out.println("实现接口的方法");
}
}
public class MyInterfaceTest {
public static void main(String[] args) {
MyInterfaceImpl impl = new MyInterfaceImpl();
impl.methodDefault1();
// impl.init();//不能调用
}
}

```

接口中的抽象方法和私有方法*只能*由接口自己调用。

五. 钻石操作符使用升级

```java
Comparator<Object> com = new Comparator<>(){
@Override
public int compare(Object o1, Object o2) {
	return 0;
}
};

//JDK 9 以前
Comparator<Object> com = new Comparator<Object>(){//有缺陷，因为可以根据前面的泛型就可以推断后面的泛型，也就得到了方法体内的类型
@Override
public int compare(Object o1, Object o2) {
	return 0;
}
};
```

六. try 语句

JDK8 中，可以实现资源的自动关闭，自动关闭的资源必须在 try 一对（）中，否则编译不通过。

```java
try(InputStreamReader reader = new InputStreamReader(System.in)){
//读取数据细节省略
}catch (IOException e){
e.printStackTrace();
}
```

JDK9 中，需要自动关闭的资源的实例化，可以在 try 的一对（）之外，此时的资源是 final 的不可修改。

```java
InputStreamReader reader = new InputStreamReader(System.in);
OutputStreamWriter writer = new OutputStreamWriter(System.out);
try (reader; writer) {
//reader是final的，不可再被赋值
//reader = null;
//具体读写操作省略
} catch (IOException e) {
e.printStackTrace();
}
```

七.  String 存储结构的变更

底层是 byte[] + 编码识别，同时 StringBuffer，和 StringBuilder 也做出相应改变

十一.  创建只读集合

```
List<String> namesList = new ArrayList <>();
namesList.add("Joe");
namesList.add("Bob");
namesList.add("Bill");
namesList = Collections.unmodifiableList(namesList);//在这里设置了该集合只读
System.out.println(namesList);

List<String> list = Collections.unmodifiableList(Arrays.asList("a", "b", "c"));
Set<String> set = Collections.unmodifiableSet(new HashSet<>(Arrays.asList("a",
"b", "c")));
```

缺点：我们在这里写了 5 行，即：它不能为单个表达式

其它方式创建的只读集合：

```
List<Integer> list = Arrays.asList(1,2,3,4,5);
```

JDk9 集合工厂方法， 创建只读集合。

```java
Map<String, Integer> map = Collections.unmodifiableMap(new HashMap<>() {
{
put("a", 1);
put("b", 2);
put("c", 3);
}
});
map.forEach((k, v) -> System.out.println(k + ":" + v));


List<Integer> list = List.of(1,2,3,4,5);//此时的 list 就是只读的
Set<Ingetr> set = Set.of(1,2,3,4,5,,6);
Map<String,Integer> = Map.of("zs",12,"ls",14,"ww",234);
Map.ofEntries(Map.Entry("zs",13),Map.Entry("ls",23));
```

九. InputStream 加强

transferTo() 方法，用于直接将数据传输到 OutputStream 

**of 方法**

十. Stream API 的加强 

- takeWhile() 返回从头开始，按照指定规则尽量的多的元素。

- dropWhile() 返回与 takeWhile() 相反剩余的元素。
- ofNullable()：形参变量是可以为null值的单个元素
- iteratre()：重载方法:这个 iterate 方法的新重载方法，可以让你提供一个 Predicate (判断条件)来指定什 么时候结束迭代。

```java
 //of()参数中的多个元素，可以包含null值
        Stream<Integer> stream1 = Stream.of(1, 2, 3,null);
        stream1.forEach(System.out::println);
        //of()参数不能存储单个null值。否则，报异常
//        Stream<Object> stream2 = Stream.of(null);
//        stream2.forEach(System.out::println);
        Integer i = 10;
        i = null;
        //ofNullable()：形参变量是可以为null值的单个元素
        Stream<Integer> stream3 = Stream.ofNullable(i);
        long count = stream3.count();
        System.out.println(count);
```

itreate 例子：

```java
// 原来的控制终止方式：
Stream.iterate(1, i -> i + 1).limit(10).forEach(System.out::println);
// 现在的终止方式：
Stream.iterate(1, i -> i < 100, i -> i + 1).forEach(System.out::println);
```

十一. Optional 获取 Stream 的方法

```java
List<String> list = new ArrayList<>();
list.add("Tom");
list.add("Jerry");
list.add("Tim");
Optional<List<String>> optional = Optional.ofNullable(list);
Stream<List<String>> stream = optional.stream();
stream.flatMap(x -> x.stream()).forEach(System.out::println);
```

Stream 是对容器的操作吗，而 Optional 也是一个容器。

十二. Javascript 引擎: Nashornn (JDK 11 被 departed)

##### 2. Java 10

局部变量类型推断

认为变量的类型声明是不必须的。 例如：`ArrayList list = new ArrayList(); //这里前面的类型声明是不必须的，因为它必定是 ArrayList、或是它的父类或接口`

```java
//1.局部变量的初始化
var i = 3;
var list = new ArrayList<>();
//2.增强 for 循环中的索引
for(var v : list) {
	System.out.println(v);
}
//3.传统for循环中
for(var i = 0;i < 100;i++) {
	System.out.println(i);
}
```

以下条件不可以使用局部类型推断

1. 没初始化的局部变量或初始值为 null：不能实现类型推断（因为是根据值来推断类型）

2. 方法的返回类型

   ```java
   public var method(){
   	return 0;
   }
   ```

   一般根据我们都是根据方法的返回类型决定可以返回什么类型；在这里的 var 就变成了返回什么都行，由里面决定外面因此不适用

3. 方法的参数类型

4. 构造器的参数类型

5. 属性

6. catch 块

7. lambda 表达式；

   ```java
   Supplier<Double> sup = () -> Math.random();//正常写法
   var sup = ()->Math.random();//错误，lambda 本质上是简写接口中的抽象方法，一旦我们连函数式接口都不知道，就不知道重写的是个抽象方法了
   ```

8. 方法引用

   ```java
   Consumer<String>  con = System.out::println();
   var con = System.out::println()://错误，跟上面的例子类似，因为我们使用 println() 方法（引用）重写了 Consumer函数式接口中的抽象方法，一旦我们连函数式接口都不知道，那么就不知道写的是什么玩意
   ```

9. 数组静态初始化

   ```java
   int[] arr = new int[]{1,2,3,4};
   var arr = new int[]{1,2,3,4};
   int[] arr = {1,2,3,4};//我们可以根据前面的类型声明来推断后面的类型
   var arr = {1,2,3,4};//错误，这样前后都没有类型，就不知道声明了个什么玩意
   ```

工作原理：

在处理 var时，编译器先是查看表达式右边部分，并根据右边变量值的类型进行推断，作为左边变量的类型，然后将该类型写入字节码当中。

注意：

- var 不是一个关键字

  var 只是一个类型名，同其它我们自定义的类型名一样，只有编译器需要知道类型的地方才需要用到它。除此之外它就是一个合法的标识符。可用作标识符

- 这不是 JavaScript 

  var 并不会改变 Java 是静态语言的事实。

**集合新增创建不可变集合的方法**

copeOf()

```java
//示例1：
var list1 = List.of("Java", "Python", "C");
var copy1 = List.copyOf(list1);
System.out.println(list1 == copy1); // true
//示例2：
var list2 = new ArrayList<String>();
var copy2 = List.copyOf(list2);
System.out.println(list2 == copy2); // false
//示例1和2代码基本一致，为什么一个为true,一个为false?
```

从源码分析，可以看 出 copyOf 方法会先判断参数集合是不是
AbstractImmutableList 类型的，如果是，就直接返回，如果不是，则调用 of 创建一个新的集合。
示例2因为用的 new 创建的集合，不属于不可变 AbstractImmutableList 类的子类，
所以 copyOf 方法又创建了一个新的实例，所以为false。
注意：使用of和copyOf创建的集合为不可变集合，不能进行添加、删除、替换、
排序等操作，不然会报 java.lang.UnsupportedOperationException 异常。
上面演示了 List 的 of 和 copyOf 方法，Set 和 Map 接口都有。

##### 3. Java11

主要：两种 GC：ZGC 和 Epsilon

字符串新增处理方法：

<img src="C:/doc/Markdown文档/Java/Java11字符串新增处理方法.png">

optional 加强

<img src="C:/doc/Markdown文档/Java/Optronal增强.png">

**局部变量类型推断升级**

在 Var 添加注解

```java
//错误的形式: 必须要有类型, 可以加上var
Consumer<String> con1 = (@Deprecated t) -> System.out.println(t.toUpperCase());
//正确的形式:
//使用var的好处是在使用lambda表达式时给参数加上注解。
Consumer<String> con2 = (@Deprecated var t) ->
System.out.println(t.toUpperCase());
```

**全新的 HTTP 客户端 API**

- HTTP/1.1依赖于请求/响应周期。 HTTP/2允许服务器“push”数据：它可以发送比客户端请求更多的数据
- HttpClient 替换 HTTPURLConnection 

**更简化的编译运行程序**

可以直接通过 java 命令来运行 Java 源程序，例如：`java HelloWorld.java`

注意：

- 执行源文件中的第一个类，第一个类必须包含主方法
- 不可以使用其它源文件中的自定义类, 本文件中的自定义类是可以使用的

**ZGC**P