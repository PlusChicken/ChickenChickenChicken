## Java面试问题第一天

1.基础不扎实

java.lang包

java.lang是提供利用Java编程语言进行程序设计的基础类。



#### Java八种基础数据类型：

byte: 8位  1个字节 -128~127

short:16位 2个字节 -2^16~2^16-1

int:32位 4个字节 -2^32~2^32-1

long:64位 8个字节 -2^64~2^64-1

float:32位 4个字节

double:64位 8个字节 

char:16位 2个字节  0~2^16-1

boolean:true和false

#### 自动转换：

byte-->short-->int-->long-->float-->double

#### 封装类自动装箱自动拆箱：

自动装箱：Integer i = Integer.valueOf(9):

自动拆箱：int i = i.intValue();

JVM会缓存-128~127的Integer对象

所以

Integer i1 = 100；

Integer i2 = 100；

i1 == i2  为true

但是

当超过JVM的缓存时，Integer就不相等了。

#### String类

String当以引号的形式出现，那么它和基本数据类型一样是保存在常量池中的

new String的对象保存在堆中

substring(int beginIndex , int endIndex)

的取值范围是[beginIndex,endIndex)

String、StringBuffer、StringBuilder的区别

(1)String是不可变字符串对象

(2)String是线程安全的，StringBuffer和StringBuilder方法和功能是等价的，但是StringBuffer是有synchronized关键词修饰的，是线程安全的，StringBuilder是非线程安全的

String str = “hello”+“world”

StringBuilder st = new StringBuilder().append("hello").append("world")

字符串改动较少的时候使用String

当字符串改动较多的时候就使用

#### System类

System类的构造器是由private修饰的，不允许被实例化，类中的方法也是由static修饰的静态方法。

三个对象：

inputSteam in；//标准输入流

PrintStream out：//标准输出流

PrintSteam err; //标准错误流

常用方法arraycopy(被复制的数组，起始位置，复制到的数组，起始位置，复制长度)

currentTimeMillis()获得当前的时间

gc()垃圾回收

exit()退出虚拟机（唯一一种能够退出程序并不执行finally的情况）

#### Exception类

try{}catch{}finally{}

try可以有多个catch

所有的异常都继承自异常类Exception，我们还可以自定义异常类，但是必须继承Exception，并添加构造方法，使用supper调用父类构造方法。

#### Java Stack类

Stack类中有

push(Object element)    将对象压入栈中

pop()   弹出顶部对象

empty()    检测堆栈是否为空

peek()   查看堆栈顶部的对象，但不从栈堆中移除它

search(Object element)   返回对象在栈中的位置，以1为基数（当有重复的对象时，只会返回第一个对象）

~~~~java
public class demo {

    static void Stackup(Stack<Integer> stk,Integer i){
        stk.push(i);
        System.out.println("加入栈中的是"+i);
        System.out.println("栈中有"+stk);
    }
    static void Stackdel(Stack<Integer> stk){
        Integer j = stk.pop();
        System.out.println("从栈中弹出的是" + j);
        System.out.println("栈中有" + stk);
    }

    public static void main(String[] args) {

        Stack<Integer> stk = new Stack();
        Stackup(stk,80);
        Stackup(stk,81);
        Stackup(stk,83);
        Stackup(stk,85);
        Stackdel(stk);
        Stackdel(stk);
        Stackdel(stk);
        Stackdel(stk);
    }
}
~~~~

#### 数据库

| id   | server_ip  | use    | user |
| ---- | ---------- | ------ | ---- |
| 0    | 172.16.0.2 | test   | jam  |
| 1    | 172.16.0.2 | dev    | jak  |
| 2    | 172.16.0.3 | online | boss |
| 3    | 172.16.0.4 | online | boss |

SELECT * FROM server_mst WHERE server_ip IN (
SELECT server_ip FROM server_mst GROUP BY server_ip HAVING COUNT(server_ip) > 1

);