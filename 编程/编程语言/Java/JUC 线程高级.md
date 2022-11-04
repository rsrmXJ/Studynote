## JUC 线程高级

##### 1. volatile 关键字与内存可见性

1\. 什么是 JUC？

JUC 就是 java.util.concurrent 工具包的简称。这是一个处理线程的工具包。since jdk 1.5

2\. JUC 解决什么问题？(为什么要有 JUC)

多线程往往意为着更大性能开销（用更多的资源做更多的事情），更快的执行速度。但是多线程使用不当时，那么性能甚至不如单线程。

3\. 内存可见性

例子：

```java
package com.atguigu.juc;

import org.apache.log4j.LogManager;
import org.apache.log4j.Logger;

public class JucTest {
    public static void main(String[] args) {
        final Logger LOGGER = LogManager.getLogger(JucTest.class);
        TheadDemo theadDemo = new TheadDemo();
        new Thread(theadDemo).start();
        while(true){
            if(theadDemo.isFlag()){
                LOGGER.debug("===============");
            }
        }

    }
}
class TheadDemo implements Runnable{
    private static final Logger LOGGER = LogManager.getLogger(TheadDemo.class);
    private boolean flag = false;


    public boolean isFlag() {
        return flag;
    }

    public void setFlag(boolean flag) {
        this.flag = flag;
    }

    @Override
    public void run() {
        try {
            Thread.sleep(200);
            flag = true;
            LOGGER.debug("flag="+isFlag());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }


    }
}
```

结果为：

```java
2021-01-11 23:29:57,0696 DEBUG (com.atguigu.juc.TheadDemo:run) - flag=true
```

并且陷入死循环中。

> 为什么会出现这种情况？
>
> 为了提高效率，JVM 会为每个线程提供缓冲区域。变量使其在每个线程中保存一份。
>
> 当我们启动了 匿名线程后陷入了休眠。然后 main 线程就获得了 flag = false。进入了它自己的缓冲区域。就算随后匿名线程将其改为 true，而 Main 线程中的 flag 也不会发生改变。

**内存可见性问题**：当多个线程操作共线数据时彼此不可见。

![img](C:\Users\XJ\Documents\Markdown文档\juc内存可见性)

这个问题以前的解决方法。

> 当操作共享数据时可以加锁，使用 synchronized 关键字。

```java
while (true){
        synchronized (threadDemo){
            if (threadDemo.isFlag()){
                System.out.println("主线程读取到的flag = " + threadDemo.isFlag());
                break;
            }
        }
 }
```

加了锁就可以使每次 while 循环都到主存中读取关键字。但是一加锁，每次只能有一个线程访问，当一个线程持有锁时，其他的就会阻塞，效率就非常低了。不想加锁，又要解决内存可见性问题，那么就可以使用 volatile 关键字。

4\. volatile 关键字

**用法**：当多个线程操作共享数据时，可以保证内存中的数据可见。用这个关键字修饰共享数据，当共享数据发生修改时，就会及时的把共享数据刷新到主存中去，同时也强制每次使用共享数据时到主存中读取，也可以理解为，就是直接操作主存中的数据。所以在不使用锁的情况下，可以使用volatile。如下：

`public  volatile boolean flag = false;`

5\. valotile 与 synchronized 的区别。

① 不具备互斥性 （当一个线程持有锁时，其他线程进不来，这就是互斥性）

② 不能保证变量的原子性

##### 2\. 原子变量和 CAS 算法

`\. **原子性**

```java
public class TestIcon {
    public static void main(String[] args){
        AtomicDemo atomicDemo = new AtomicDemo();
        for (int x = 0;x < 10; x++){
            new Thread(atomicDemo).start();
        }
    }
}

class AtomicDemo implements Runnable{
    private int i = 0;
    public int getI(){
        return i++;
    }
    @Override
    public void run() {
        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(getI());
    }
}
//在run方法里面让i++，然后启动十个线程去访问
```

根据运行结果，可以发现，出现了重复数据。明显产生了多线程安全问题，或者说原子性问题。所谓原子性就是操作不可再细分，而i++操作分为读改写三步，如下：

```java
int temp = i;
i = i + 1;
i = temp;//想不明白，这里既然对 i 进行了赋值之后，那么不久相当于值并没有发生改变，那么如果确保下次使用到 i 时值为 i + 1;
//i++是一个多步操作，而且是可以被中断的；i++可以被分割成3步，第一步读取 i 的值，第二步计算 i+1；第三部将最终值赋值给i
```

所以 i++ 明显不是原子操作。上面10个线程进行i++时，内存图解如下：

![img](C:\Users\XJ\Documents\Markdown文档\juci++)

看到这里，好像和上面的内存可见性问题一样。是不是加个 volatile 关键字就可以了呢？其实不是的，因为加了 volatile，只是相当于所有线程都是在主存中操作数据而已，但是不具备互斥性。比如两个线程同时读取主存中的 0，然后又同时自增，同时写入主存，结果还是会出现重复数据。

原子变量：JDK 1.5 之后，Java 提供了原子变量，在 java.util.concurrent.atomic 包下。原子变量具备如下特点：

① 有 volatile 保证内存可见性

② 用 CAS (Compare -And-Swap) 算法保证原子性

​	CAS 算法时硬件对并发操作共享数据的支持

2\. CAS 算法

CAS 是 Compare and swap 的简称，这个操作是硬件级别的操作，在硬件层面保证了操作的原子性。

当且仅当预期值A和	CAS 包含了三个操作数：

- 内存值 V

- 旧的预期值 A

- 要修改的新值 B

当且仅当预期值 A 和内存值 V 相同时，将内存值 V 修改为 B，否则什么都不做。

> 当多个线程并发对主存中的数据进行修改时，只有线程会成功，其他线程皆会失败。

```java
public class TestIcon {
    public static void main(String[] args){
        AtomicDemo atomicDemo = new AtomicDemo();
        for (int x = 0;x < 10; x++){
            new Thread(atomicDemo).start();
        }
    }
}

class AtomicDemo implements Runnable{
    //private int i = 0;
    private AtomicInteger i = new AtomicInteger();
    public int getI(){
        return i.getAndIncreament();
    }
    @Override
    public void run() {
        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(getI());
    }
}
```

##### 3\.  模拟 CAS 算法

```java
/*
 * 模拟 CAS 算法
 */
public class TestCompareAndSwap {

	public static void main(String[] args) {
		final CompareAndSwap cas = new CompareAndSwap();
		
		for (int i = 0; i < 10; i++) {
			new Thread(new Runnable() {
				
				@Override
				public void run() {
					int expectedValue = cas.get();//首先获取旧的期望值
                      //然后看旧的期望值和预期值是否相同，如果相同就改，不同就不改
					boolean b = cas.compareAndSet(expectedValue, (int)(Math.random() * 101));
					System.out.println(b);
				}
			}).start();
		}
		
	}
	
}

class CompareAndSwap{
	private int value;
	
	//获取内存值
	public synchronized int get(){
		return value;
	}
	
	//比较
	public synchronized int compareAndSwap(int expectedValue, int newValue){
		int oldValue = value;//内存值
		if(oldValue == expectedValue){
			this.value = newValue;
		}
		
		return oldValue;
	}
	
	//设置
	public synchronized boolean compareAndSet(int expectedValue, int newValue){
		return expectedValue == compareAndSwap(expectedValue, newValue);
	}
}
```

##### 4\. 同步容器类 ConcurrentHashMap

ConcurrentHashMap 采用锁分段机制

JDK 1.5之后，在 java.util.concurrent 包中提供了多种并发容器类来改进同步容器类的性能。其中最主要的就是ConcurrentHashMap。

