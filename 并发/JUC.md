---
typora-copy-images-to: img_juc
typora-root-url: img_juc
---

# JUC

## 一、volatile关键字和内存可见性

### 1.内存可见性

内存可见性（Memory Visibility）是指当某个线程正在使用对象状态 而另一个线程在同时修改该状态，需要确保当一个线程修改了对象 状态后，其他线程能够看到发生的状态变化。

可见性错误是指当读操作与写操作在不同的线程中执行时，我们无法确保执行读操作的线程能适时地看到其他线程写入的值，有时甚至是根本不可能的事情。(多个线程操作共享数据时，彼此不可见-->使用同步锁解决)	

我们可以通过同步锁来保证对象被安全地发布。除此之外我们也可以使用一种更加**轻量级**的volatile 变量。

### 2.Volatile（保证内存可见性）

当多个线程操作共享数据时，可以保证内存中的数据可见（通过底层的内存栅栏实现的，时时刻刻把缓存中的数据刷到主存中）

Volatile比锁的效率高，比什么都不加的效率要低

> **Volatile关键字**，Java 提供了一种稍弱的同步机制，即 volatile 变 量，用来确保将变量的更新操作通知到其他线程。 可以将volatile 看做一个轻量级的锁，但是又与 锁有些不同：
>
> - 对于多线程，不是一种互斥关系
> - 不能保证变量状态的“原子性操作”

Volatile相较于Synchronized是一种较为轻量级的同步策略

```java
package com.xfnlp.JUC;



/*
 * 一、volatile 关键字：当多个线程进行操作共享数据时，可以保证内存中的数据可见。
 *                   相较于 synchronized 是一种较为轻量级的同步策略。
 *
 * 注意：
 * 1. volatile 不具备“互斥性”
 * 2. volatile 不能保证变量的“原子性”
 */
public class TestVolatile {

    public static void main(String[] args) {
        ThreadDemo td = new ThreadDemo();
        new Thread(td).start();

        while(true){
            if(td.isFlag()){
                System.out.println("------------------");
                break;
            }
        }

    }

}

class ThreadDemo implements Runnable {

    private volatile boolean flag = false;

    @Override
    public void run() {

        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
        }

        flag = true;

        System.out.println("flag=" + isFlag());

    }

    public boolean isFlag() {
        return flag;
    }

    public void setFlag(boolean flag) {
        this.flag = flag;
    }

}
```

## 二、原子变量和CAS算法

​		Java的`java.util.concurrent`包除了提供底层锁、并发集合外，还提供了一组原子操作的封装类，它们位于`java.util.concurrent.atomic`包。Atomic类是通过无锁（lock-free）的方式实现的线程安全（thread-safe）访问。它的主要原理是利用了CAS：Compare and Set。原子变量有volatile的特性，保证内存可见性，CAS（Compare-And-Swap）算法保证数据的原子性

​		CAS (Compare-And-Swap) 是一种硬件对并发操作共享数据的支持，针对多处理器 操作而设计的处理器中的一种特殊指令，用于管理对共享数据的并发访问。

CAS 是一种无锁的非阻塞算法的实现

CAS 包含了 3 个操作数：

- 需要读写的内存值V
- 进行比较的值A
- 拟写入的新值B

当且仅当V 的值等于A 时，CAS 通过原子方式用新值B 来更新V 的 值，否则不会执行任何操作

**CAS算法比同步锁效率高很多**

```java
package com.atguigu.juc;

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
					int expectedValue = cas.get();
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
		int oldValue = value;
		
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



## 三、ConcurrentHashMap 锁分段机制

- Java 5.0 在```java.util.concurrent ```包中提供了多种并发容器类来改进同步容器 的性能。
- ```ConcurrentHashMap ```同步容器类是Java 5 增加的一个线程安全的哈希表。对 与多线程的操作，介于HashMap 与 Hashtable 之间。内部采用“**锁分段**” 机制替代``` Hashtable ```(HashTable锁整个表)的独占锁。进而提高性能。
  - HashTable符合操作也是线程不安全的（若存在则删除，若不存在则添加）
- 此包还提供了设计用于多线程上下文中的 Collection 实现： ```ConcurrentHashMap、ConcurrentSkipListMap、ConcurrentSkipListSet、 CopyOnWriteArrayList ```和 ```CopyOnWriteArraySet```。当期望许多线程访问一个给 定``` collection``` 时，```ConcurrentHashMap ```通常优于同步的```HashMap```， ```ConcurrentSkipListMap ```通常优于同步的 ```TreeMap```。当期望的读数和遍历远远 大于列表的更新数时，```CopyOnWriteArrayList ```优于同步的```ArrayList```。

https://www.cmsblogs.com/article/8282727273

```java
package com.atguigu.juc;

import java.util.Iterator;
import java.util.concurrent.CopyOnWriteArrayList;

/*
 * CopyOnWriteArrayList/CopyOnWriteArraySet : “写入并复制”
 * 注意：添加操作多时，效率低，因为每次添加时都会进行复制，开销非常的大。并发迭代操作多时可以选择。
 */
public class TestCopyOnWriteArrayList {

	public static void main(String[] args) {
		HelloThread ht = new HelloThread();
		
		for (int i = 0; i < 10; i++) {
			new Thread(ht).start();
		}
	}
	
}

class HelloThread implements Runnable{
	
//	private static List<String> list = Collections.synchronizedList(new ArrayList<String>());
	
	private static CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
	
	static{
		list.add("AA");
		list.add("BB");
		list.add("CC");
	}

	@Override
	public void run() {
		
		Iterator<String> it = list.iterator();
		
		while(it.hasNext()){
			System.out.println(it.next());
			
			list.add("AA");
		}
		
	}
	
}
```

## 四、CountDownLatch 闭锁

- Java 5.0 在 java.util.concurrent 包中提供了多种并发容器类来改进同步容器 的性能。
- CountDownLatch 一个同步辅助类，在完成一组正在其他线程中执行的操作 之前，它允许一个或多个线程一直等待。
- 闭锁可以延迟线程的进度直到其到达终止状态，闭锁可以用来确保某些活 动直到其他活动都完成才继续执行：
  - 确保某个计算在其需要的所有资源都被初始化之后才继续执行;
  - 确保某个服务在其依赖的所有其他服务都已经启动之后才启动;
  - 等待直到某个操作所有参与者都准备就绪再继续执行。

闭锁：在完成某些运算时，只有其他所有线程的运算全部完成，当前运算才继续执行

```java
package com.atguigu.juc;

import java.util.concurrent.CountDownLatch;

/*
 * CountDownLatch ：闭锁，在完成某些运算是，只有其他所有线程的运算全部完成，当前运算才继续执行
 */
public class TestCountDownLatch {

	public static void main(String[] args) {
		final CountDownLatch latch = new CountDownLatch(50);
		LatchDemo ld = new LatchDemo(latch);
        
		long start = System.currentTimeMillis();
        
		for (int i = 0; i < 50; i++) {
			new Thread(ld).start();
		}
		try {
			latch.await();
		} catch (InterruptedException e) {
		}
        
		long end = System.currentTimeMillis();
		System.out.println("耗费时间为：" + (end - start));
	}

}

class LatchDemo implements Runnable {

	private CountDownLatch latch;

	public LatchDemo(CountDownLatch latch) {
		this.latch = latch;
	}

	@Override
	public void run() {

		try {
			for (int i = 0; i < 50000; i++) {
				if (i % 2 == 0) {
					System.out.println(i);
				}
			}
		} finally {
			latch.countDown();
		}

	}

}
```

## 五、Callable接口

- Java 5.0 在 java.util.concurrent 提供了一个新的创建执行 线程的方式：Callable 接口
- Callable 接口类似于Runnable，两者都是为那些其实例可 能被另一个线程执行的类设计的。但是 Runnable 不会返 回结果，并且无法抛出经过检查的异常。
- Callable 需要依赖FutureTask ，FutureTask也可以用作闭锁。

```java
package com.xianfeng.demo01;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;


/**
 * 创建执行线程的方式三：实现Callable接口,相较于实现RUnable接口的方式，方法可以有返回值，并且可以抛出异常
 * 执行Collable方式，需要FutureTask实现类的支持，用于接受运算结果FutureTask是Future接口的实现类
 */
public class TestThread2 {

    public static void main(String[] args) {
        ThreadDemo td = new ThreadDemo();
        //执行Collable方式，需要FutureTask实现类的支持，用于接受运算结果
        FutureTask<Integer>  result = new FutureTask<>(td);

        new Thread(result).start();

         //接受线程运算结果
        try {
            Integer sum = result.get();    //FutureTask也可以用于闭锁的操作
            System.out.println(sum);
            System.out.println("=============================");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }

    }



}

class ThreadDemo implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        int sum = 0;
        for (int i = 0; i <= Integer.MAX_VALUE; i++) {
            sum += i;
        }
        return sum;
    }
}

```

## 六、Condition线程通信



## 七、线程按序交替

> 编写一个程序，开启 3 个线程，这三个线程的ID 分别为 A、B、C，每个线程将自己的ID 在屏幕上打印 10 遍，要 求输出的结果必须按顺序显示。
>
> 如：ABCABCABC…… 依次递归

```java
package com.xfnlp.JUC;



import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * 编写一个程序，开启 3 个线程，这三个线程的ID 分别为
 * A、B、C，每个线程将自己的ID 在屏幕上打印 10 遍，要
 * 求输出的结果必须按顺序显示。
 */
public class TestABCAlternate {
    public static void main(String[] args) {
        AlternateDemo ad = new AlternateDemo();

        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 20; i++) {
                    ad.loopA(i);
                }
            }
        },"A").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 20; i++) {
                    ad.loopB(i);
                }
            }
        },"B").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 20; i++) {
                    ad.loopC(i);
                    System.out.println("--------------------------");
                }
            }
        },"C").start();

    }
}


class AlternateDemo{

    private int number = 1;//当前正在执行线程的标记

    private Lock lock = new ReentrantLock();
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();

    public void loopA(int totalLoop){
        lock.lock();

        try {
            //1.判断
            if(number != 1){
                condition1.await();
            }
            else{//2.打印
                for (int i = 0; i < 5; i++) {
                    System.out.println(Thread.currentThread().getName() + "\t" + i + "\t" + totalLoop);
                }
                //3.唤醒
                number = 2;
                condition2.signal();
            }


        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public void loopB(int totalLoop){
        lock.lock();

        try {
            //1.判断
            if(number != 2){
                condition2.await();
            }
            else{//2.打印
                for (int i = 0; i < 5; i++) {
                    System.out.println(Thread.currentThread().getName() + "\t" + i + "\t" + totalLoop);
                }
                //3.唤醒
                number = 3;
                condition3.signal();
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public void loopC(int totalLoop){
        lock.lock();

        try {
            //1.判断
            if(number != 3){
                condition3.await();
            }
            else{//2.打印
                for (int i = 0; i < 5; i++) {
                    System.out.println(Thread.currentThread().getName() + "\t" + i + "\t" + totalLoop);
                }
                //3.唤醒
                number = 1;
                condition1.signal();
            }


        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}
```

## 八、ReadWriteLock 读写锁

​		ReadWriteLock 维护了一对相关的锁，一个用于只读操作， 另一个用于写入操作。只要没有writer，读取锁可以由 多个reader 线程同时保持。写入锁是独占的。

​		ReadWriteLock 读取操作通常不会改变共享资源，但执行 写入操作时，必须独占方式来获取锁。对于读取操作占 多数的数据结构。 ReadWriteLock 能提供比独占锁更高 的并发性。而对于只读的数据结构，其中包含的不变性 可以完全不需要考虑加锁操作。

```java
package com.xfnlp.JUC;

import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

/*
 * 1. ReadWriteLock : 读写锁
 * 
 * 写写/读写 需要“互斥”
 * 读读 不需要互斥
 * 
 */
public class TestReadWriteLock {

   public static void main(String[] args) {
      ReadWriteLockDemo rw = new ReadWriteLockDemo();
      
      new Thread(new Runnable() {
         
         @Override
         public void run() {
            rw.set((int)(Math.random() * 101));
         }
      }, "Write:").start();
      
      
      for (int i = 0; i < 100; i++) {
         new Thread(new Runnable() {
            
            @Override
            public void run() {
               rw.get();
            }
         }).start();
      }
   }
   
}

class ReadWriteLockDemo{
   
   private int number = 0;
   
   private ReadWriteLock lock = new ReentrantReadWriteLock();
   
   //读
   public void get(){
      lock.readLock().lock(); //上锁
      
      try{
         System.out.println(Thread.currentThread().getName() + " : " + number);
      }finally{
         lock.readLock().unlock(); //释放锁
      }
   }
   
   //写
   public void set(int number){
      lock.writeLock().lock();
      
      try{
         System.out.println(Thread.currentThread().getName());
         this.number = number;
      }finally{
         lock.writeLock().unlock();
      }
   }
}
```

九、线程八锁

> ```java
> /*
>  * 题目：判断打印的 "one" or "two" ？
>  * 
>  * 1. 两个普通同步方法，两个线程，标准打印， 打印? //one  two
>  * 2. 新增 Thread.sleep() 给 getOne() ,打印? //one  two
>  * 3. 新增普通方法 getThree() , 打印? //three  one   two
>  * 4. 两个普通同步方法，两个 Number 对象，打印?  //two  one
>  * 5. 修改 getOne() 为静态同步方法，打印?  //two   one
>  * 6. 修改两个方法均为静态同步方法，一个 Number 对象?  //one   two
>  * 7. 一个静态同步方法，一个非静态同步方法，两个 Number 对象?  //two  one
>  * 8. 两个静态同步方法，两个 Number 对象?   //one  two
>  */
> ```

```java
package com.xfnlp.JUC;

public class TestThread8Monitor {
   
   public static void main(String[] args) {
      Number number = new Number();
      Number number2 = new Number();
      
      new Thread(new Runnable() {
         @Override
         public void run() {
            number.getOne();
         } 
      }).start();
      
      new Thread(new Runnable() {
         @Override
         public void run() {
//          number.getTwo();
            number2.getTwo();
         }
      }).start();
      
      /*new Thread(new Runnable() {
         @Override
         public void run() {
            number.getThree();
         }
      }).start();*/
      
   }

}

class Number{
   
   public static synchronized void getOne(){//Number.class
      try {
         Thread.sleep(3000);
      } catch (InterruptedException e) {
      }
      
      System.out.println("one");
   }
   
   public synchronized void getTwo(){//this
      System.out.println("two");
   }
   
   public void getThree(){
      System.out.println("three");
   }
   
}
```

线程八锁的关键：
 * ①非静态方法的锁默认为  this,  静态方法的锁为 对应的 Class 实例
 * ②某一个时刻内，只能有一个线程持有锁，无论几个方法。
