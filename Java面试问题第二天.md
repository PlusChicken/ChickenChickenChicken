## Java面试问题第二天

#### java.lang包

最重要的类是Object（类层次结构的根）和Class（它的实例表示正在运行的应用程序中的类）

还有的包装类是Boolean、Character、Byte、Short、Integer、Long、Float和Double。Void类是一个非实例化的类，它保持一个对表示基本类型void的Class对象的引用。

字符串处理类型：String类，StringBuffer类；

线程类：Thread类，ThreadDeath类、Runnable类；

错误和异常处理类：Throwable类、Exception类、Error类；

数学类：Math类；

过程类：Process类

系统和运行类：System类、Runtime类；

操作类：ClassLoader类

### Thread类

线程和进程的关系： 一个进程可以有多个线程，每条线程执行不同的任务，不同的进程使用不同的内存空间，而所有的线程共享一片相同的内存空间。都拥有单独的栈内存来存储本地数据。

新建（New）-->就绪（Runnable）-->运行（Running）-->阻塞（Blocked）-->死亡（Dead）

互斥条件：一个资源每次只能被一个资源使用

请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。

不剥夺条件：进程已获得的资源，在未使用完之前，不能强行剥夺。

循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系。

活锁：是前者的状态可以改变，但是无法继续执行

线程的三种实现方式：继承Thread，实现runable接口，实现callable接口

竞争条件：竞争条件会触发很多的bug，因为当某个线程需要通过某个变量的状态来执行相应的程序，但是该变量已经被其他线程修改。这种bug是很难发现的。线程是随机竞争的。

同步块锁：

public static synchronized void mA(){...}//锁对象为类名.class

public synchronized void mB(){...}//锁的对象是this

public void mC(){

​		synchronized(类名.class){...}

}//锁的对象是类名.class

public void mD(){

​		synchronized(this.getClass()){...}

}//锁的对象是类名.class

public void mE(){

​		synchronized(this){...}

}//锁的对象是this

public void mF(){

​		synchronized（lock）{

​				lock.wait();

}

}//锁的对象是lock

public void mG(){

​		synchronized(lock){

​				lock.notifyAll();

​		}

}//锁的对象是lock

锁和同步块的区别：

1. 两者相比，锁更加灵活
2. lock支持tryLock()方法，没有获取锁是可以继续执行不阻塞
3. 支持读写分离，多线程读，一个线程写，reentrantreadwritelock

ReentrantLock是Lock的唯一实现类

Lock接口提供的synchronized关键字锁不具备的主要特性有：

1. 尝试非阻塞地获取锁，当前线程获取锁时，如果锁没有被其他线程获取到，则成功获取并持有锁；
2. 被中断地获取锁，与synchronized不同，获取到锁的线程能响应中断，当获取到锁的线程被中断时，会抛出中断异常，并释放锁；
3. 超时获取锁，在指定的截止时间前获取锁，如果时间到了仍未获取到锁，则返回；

~~~~java
//获取随机数[0，n)
Random.nextInt(int n)
//获取当前线程对象（就是这个线程本身）
Thread.currentThread
~~~~



#### 读写分离锁ReadWriteLock(今日重点)

~~~~java
package com.little;

import java.util.Random;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class demo{
    //实例化读写对象
    private static ReentrantReadWriteLock readWriteLock = new ReentrantReadWriteLock();
    private static Lock readLock = readWriteLock.readLock();
    private static Lock writeLock = readWriteLock.writeLock();

    private int value = 0;

    //读线程
    public Object handleRead(Lock lock) throws InterruptedException{
      try {
          lock.lock();
          Thread.sleep(1000);
          System.out.println(value);
          return value;
      }finally {
          lock.unlock();
      }
    }

    //写线程
    public void handleWrite(Lock lock,int tmp) throws InterruptedException{
        try {
            lock.lock();
            Thread.sleep(1000);
            System.out.println("value写入=" + tmp);
            value = tmp;
        }finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) {
        final demo RWL = new demo();
        Thread RT1 = new Thread(() -> {
            try{
                RWL.handleRead(readLock);
            }catch (Exception e){
                e.printStackTrace();
            }
        });

        Thread RT2 = new Thread(() -> {
            try {
                RWL.handleWrite(writeLock,new Random().nextInt());
            }catch (Exception e){
                e.printStackTrace();
            }
        });
        for (int i = 0; i < 2; i++) {
            new Thread(RT2).start();
        }
        for (int j = 0; j < 10; j++) {
            new Thread(RT1).start();
        }
    }
}
~~~~



#### tryLock使用方式

~~~~java
Lock lock = new ReentrantLock();
    public static void main(String[] args) {
        //这是类名
        demo dem = new demo();
        new Thread(() -> dem.runThread(Thread.currentThread())).start();
        new Thread(() -> dem.runThread(Thread.currentThread())).start();
    }

    public void runThread(Thread t){
        try {
            if (lock.tryLock()){
                System.out.println("线程" + t.getName() + "获取锁成功");
                try{
                    //当注释掉下面这句话时
                    //线程Thread-0获取锁成功
                    //线程Thread-0释放锁
                    //线程Thread-1获取锁成功
                    //线程Thread-1释放锁
                    Thread.sleep(5000);
                }catch (Exception e){
                    e.printStackTrace();
                }finally {
                    System.out.println("线程"+t.getName()+"释放锁");
                    lock.unlock();
                }
            }else{
                System.out.println("线程"+t.getName()+"获取锁失败");
            }
        } catch (Exception e){
            e.printStackTrace();
        }
    }
//线程Thread-0获取锁成功
//线程Thread-1获取锁失败
//线程Thread-0释放锁
~~~~



#### Vector类(集合包)

实现队列

~~~~~java
    public static void push(Vector vector,Object o){
        vector.addElement(o);
        System.out.println(vector);
    }
    public static Vector creatqueue(){
        Vector vector = new Vector(0);
        System.out.println("队列创建成功");
        return vector;
    }
    public static Object  pop(Vector vector){
        Object o ;
        o = vector.remove(0);
        System.out.println(vector);
        System.out.println(o);
        return o;
    }
    public static void main(String[] args) {
        Vector vector = creatqueue();
        push(vector,1);
        push(vector,4);
        push(vector,3);
        push(vector,2);
        pop(vector);
        pop(vector);
        pop(vector);
        pop(vector);

    }
~~~~~

