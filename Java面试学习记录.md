## Java面试学习记录

### GC（Generational Collection）

年轻代、年老代、持久代

年轻代：年老代=1 : 2

年轻代分为eden : from : to=8 : 1 : 1

年轻代eden区存满之后利用复制算法将eden的存活对象复制到from中，然后当eden和from都满之后就将from和eden中的存活对象复制到to中，然后清空eden和from，之后就是eden与to区的互动，存满之后复制到from区中。当存活对象在年轻代的时间到达一定的阈值时，会被复制到年老代，这就是Minor GC ，发生的频率比较高。

当年老代内存满时会触发Full GC，发生的频率比较低

Java8将永久代删除，引入新的native内存专区块，Metaspace（元数据区域）都是对于JVM规范中方法区的实现，元数据空间不存在于虚拟机中，而是使用本地内存

GC(垃圾收集器)

新生代收集器使用的收集器：Serial、PraNew、Parallel Scavenge

老年代收集器使用的收集器：Serial Old、Parallel Old、CMS

Serial收集器(复制算法)

新生代单线程收集器，标记和清理都是单线程，有点是简单高效。

Serial Old收集器(标记-整理算法)

老年代单线程收集器，Serial收集器的老年代版本。

ParNew收集器(停止-复制算法)

新生代收集器，可以认为是Serial收集器的多线程版本，在多核CPU环境下有着比Serial更好的表现。

Parallel Scavenge收集器(停止-复制算法)

并行收集器，追求高吞吐量，高效利用CPU。吞吐量一般为99%，吞吐量=用户线程时间/(用户线程时间+GC线程时间)。适合后台应用等对交互相应要求不高的场景。

CMS(Concurrent Mark Sweep)收集器(标记-清理算法)

高并发、低停顿，追求最短GC回收停顿时间，cpu占用比较高，响应时间块，停顿时间短，多核cpu追求高响应时间的选择。

### JVM虚拟机

JVM虚拟机主要包括程序计数器、虚拟机栈、本地方法栈、堆和方法区。

**程序计数器**和**虚拟机栈**都是线程“私有”的内存

**程序计数器**是一块比较小的内存空间，主要存放代码执行的位置、分支、循环、跳转、异常处理、线程恢复等基础功能，需要依赖这个计数器来完成。

例如，多线程中，为了线程切换后能够恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器，他们互不影响，独立存储。

**虚拟机栈**中存放了各种基本数据类型和对象引用。比如int i。这个时候虚拟机会在栈中分配一个int类型的内存给i，并初始化为零值。本地方法栈和虚拟方法栈所发挥的作用是非常相似的，**他们之间的区别不过是虚拟机栈为虚拟机执行java方法服务，而本地方法栈则为虚拟机使用到Native方法服务**。

堆和方法区是各个线程共享的内存区域

**堆**(Java Heap)是虚拟机所管理的内存中最大的一块，在虚拟机启动时创建。此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例都在这里分配内存，但是随着JIT编译器的发展和逃逸分析技术逐渐成熟，栈上分配、标量替换优化技术将会导致一些微妙的变化发生，所有的对象都会分配在堆上就变得不是这么绝对了。

方法区：用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

运行时常量池是方法区的一部分，用于存放编译器生成的各种字面量的符号引用。

堆：对象

本地栈：Native

虚拟栈：基础数据类型，对象引用

方法区：用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据

方法区和堆时各个线程共享的内存区域

程序计数器和虚拟栈时线程私有的

<hr>

### Object中的方法

.equals

.clone

.hashCode

.notify

.notifyall

.wait

.wait(long timeout)

.wait(long timeout , int nanos)

.getclass

.toString

.finalize

<hr>

## 向上转型

向上转型是

Animal animal = new Cat（）；

向下转型是

Cat cat = (Cat)animal ;

<hr>

## Java对象的四种引用：强引用、软引用、弱引用和虚引用

被GC回收的优先级：强引用>软引用>弱引用>虚引用

软引用、弱引用和虚引用都是基于强引用的

强引用：

~~~~java
Student student = new Student();
~~~~

软引用：

~~~~java
Student student = new Student();
SoftReference softReference = new SoftReference(student);
~~~~

弱引用：

~~~~java
Student student = new Student();
WeakReference weakReference = new WeakReference(student);
~~~~

虚引用：

~~~~java
ReferenceQueue referenceQueue = new ReferenceQueue();
PhantomReference phantomReference = new PhantomReference(Object,queue);
~~~~

<hr>

## 反射

反射是Java的特征之一，它允许运行中的Java程序获取自身的信息，并且可以操作类或对象的内部属性。

反射机制的功能：

1. 能够在运行时判断任意对象所属的类；
2. 能够在运行时创建任意类的对象；
3. 能够在运行时判断任意类所具有的局部变量和方法；
4. 能够在运行时调用任意对象的方法。

<hr>

## 抽象类和接口的区别

| 类型       |                        abstract class                        |                          interface                           |
| ---------- | :----------------------------------------------------------: | :----------------------------------------------------------: |
| 定义       |                     abstract class关键字                     |                       interface关键字                        |
| 继承       |  抽象类可以继承一个类和实现多个接口；子类只能继承一个抽象类  | 接口只可以继承接口(一个或者多个)(用的是extend而不是interface)；子类可以实现多个接口 |
| 访问修饰符 |      抽象方法可以有public、protected和default这些修饰符      |      接口方法默认修饰符是public，你不可以使用其他修饰符      |
| 方法实现   |           可定义构造方法，可以有抽象方法和具体方法           | 接口完全是抽象的，没有构造方法，且方法都是抽象的，不存在方法的实现 |
| 实现方法   | 子类使用extends关键字来继承抽象类。如果子类不是抽象类的话，它需要提供抽象类中所有声明的方法的实现 | 子类使用关键字implements来实现接口。它需要提供接口中所有声明的方法的实现 |
| 作用       |               为了把相同的东西提取出来，即重用               |         为了把程序模块进行固化的契约，是为了降低耦合         |

抽象类可以继承实体类，Object就是实体类

<hr>

## final、finally、finalize

final修饰类的时候就说明类是不能被继承的

final修饰变量的时候说明变量不能修改

final修饰方法的时候就说明方法不能被重写

finally是与try联合使用，当try模块执行时，finally模块无论如何都会执行

finalize时JavaGC时会调用的方法

## 单例模式

枚举

public enum Singleton{
	INSTANCE;
	public void whateverMethod(){

​	}

}

<hr>

饿汉模式

public class Singleton{

​	private static Singleton instance = new Singleton();

​	private Singleton(){}

​	public static Singleton getInstance(){

​	return instance;

​	}

}

