---
layout: post
title:  "Java Memory Model"
date:   2018-07-17 15:26:45
category: concurrent
published: true
author: RoyChan
tags:
- java
- concurrent
---

# 线程通讯
并发编程的核心问题是线程通信，以此作为分类的两种通信模型是共享内存和消息传递，而JMM是以共享内存为基础的内存管理模型，两种模型的区别如下：
* 共享内存：线程通过写-读内存中的公共状态隐式通信
* 消息传递：线程通过明确的发送消息来显式通信

![message_model](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_message_model.png)

# 重排序
编译器和处理器会对指令做重排序，目的是在执行程序时提高性能，重排序的三种类型：
1. 编译器优化的重排序，不改变单线程语义的前提下，可以重新安排语句的执行顺序，属于编译器重排序。
2. 指令级并行的重排序，在不存在数据依赖性，处理器可以改变语句对应机器指令的执行顺序，属于处理器重排序。
3. 内存系统的重排序，由于处理器使用缓存和读/写缓冲区，这使得加载和存储操作看上去可能是在乱序执行，属于处理器重排序。

从java源码到最终执行的指令序列，分别经历下面三种重排序：

![reorder](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_reorder.png)


# 处理器重排序
现代处理器用写缓冲区来临时保存向内存写入的数据。好处是避免停顿延迟和减少对内存总线的占用，但写缓冲区仅仅对它所在的处理器可见。所以会对执行顺序产生重要影响：处理器对内存的读/写操作的执行顺序，不一定与内存实际发生的读/写操作一致，示例如下：
![processor_reorder_1](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_processor_reorder_1.png)

![processor_reorder_2](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_processor_reorder_2.png)


# 内存屏障
java编译器在生成指令序列的适当位置会插入内存屏障指令来禁止特定类型的处理器重排序。内存屏障指令分为下列四类：

| 屏障类型 | 指令示例| 说明 |
|:--------|:-------:|:--------|
| LoadLoad Barriers| Load1;LoadLoad;Load2 | 确保Load1数据的装载，之前于Load2及所有后续装载指令的装载 |
| StoreStore Barriers | Store1;StoreStore;Store2 | 确保Store1数据对其他处理器可见（刷新到内存），之前于Store2及所有后续存储指令的存储 |
| LoadStore Barriers | Load1;LoadStore;Store2 | 确保Load1数据装载，之前于Store2及所有后续存储指令的存储 |
| StoreLoad Barriers| Store1;StoreLoad;Load2 |确保Store1数据对其他处理器可见（刷新到内存），之前于Load2及所有后续存储指令的装载，StoreLoad Barriers会使该屏障之前的所有内存访问指令（存储和装载指令）完成之后，才执行改屏障之后的内存访问指令|

StoreLoad Barriers是一个“全能型”的屏障，它同时具有其他三个屏障的效果。因为当前处理器通常要把缓冲区中的数据全部刷新到内存中，所有执行该屏障开销会很大。

# happens-before
在JMM中，一个操作执行的结果需要对另一个操作可见，那么这两个操作之间必须要存在happens-before关系。这两个操作既可以是同一线程内的，也可以是不同线程间的。规则如下：
* 程序顺序规则：线程中的每个操作happens-before任意后续操作
* 监视器锁规则：对一个监视器的解锁happens-before于随后对这个监视器的加锁
* volatile变量规则：对一个volatile域的写happens-before于任意后续对这个volatile域的读
传递性：如果A happens-before B，且B happens-before C，那么A happens-before C

# 数据依赖性
如果两个操作访问同一个变量，且这两个操作中有一个为写操作，此时这两个操作之间就存在数据依赖性。分为下列三种类型：
![data_dependence](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_data_dependence.png)

注：数据依赖性仅针对单个处理器中执行的指令序列和单个线程中执行的操作

# as-if-serial语义
语义：不管怎么重排序（单线程）程序的执行结果不能被改变。为了遵守as-if-serial语义，编译器和处理器不会对**存在数据依赖关系的操作**做重排序，因为这种重排序会改变执行结果。
```java
double pi = 3.14;//A
double r = 1.0;//B
double area = pi * r * r;//C
```
数据依赖关系如下

![as-if-serial_1](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_as-if-serial_1.png)

两种执行顺序

![as-if-serial_2](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_as-if-serial_2.png)

# 顺序一致性
### 数据竞争
当程序未正确同步时，就可能会存在数据竞争。数据竞争定义如下：
* 在一个线程中写一个变量
* 在另一个线程中读同一个变量
* 写和读没有通过同步来排序

### 内存模型
顺序一致性模型是理论参考模型，其特性如下：
* 一个线程中的所有操作必须按照程序的顺序来执行。
* （不管程序是否同步）所有线程都只能看到一个单一的操作执行顺序。在顺序一致性内存模型中，每个操作都必须原子执行且立刻对所有线程可见。

JMM对正确同步的多线程程序的内存一致性做了保证：
* 如果程序是正确同步的，程序的执行将具有顺序一致性--即程序的执行结果与该程序在顺序一致性内存模型中的执行结果相同。

![memory_model_1](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_memory_model_1.png)

在概念上，顺序一致性模型有一个单一的全局内存，这个内存通过一个左右摆动的开关可以连接到任意一个线程，同时每一个线程必须按照程序的顺序来执行内存读/写操作。如上图，在任意时间点最多只能有一个线程可以连接到内存。当多个线程并发执行时，图中的开关装置能把所有线程的所有内存读/写操作串行化（即在顺序一致性模型中，所有操作之间具有全序关系）

假设线程A中有三个操作，顺序是：A1->A2->A3，线程B中有三个操作，顺序是：B1->B2->B3。假设这两个线程使用监视器锁来正确同步：A线程的三个操作执行后释放监视器锁，随后B线程获取同一个监视器锁。执行效果如下

![memory_model_2](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_memory_model_2.png)

未执行同步的效果如下

![memory_model_3](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_memory_model_3.png)

# volatile
对一个volatile变量的单个读/写操作，与对一个普通变量的读/写操作使用同一个锁来同步的效果相同。
```java
class VolatileFeaturesExample{
    volatile long vl = 0L;
    
    public void set(long l){
        vl = l;
    }
    
    public void getAndIncrement(){
        vl++;
    }
    
    public long get(){
        return vl;
    }
}
```

```java
class VolatileFeaturesExample{
    volatile long vl = 0L;
    
    public synchronized void set(long l){
        vl = l;
    }
    
    public void getAndIncrement(){
        long temp = get();
        temp += 1L;
        set(temp);
    }
    
    public synchronized long get(){
        return vl;
    }
}
```

特性
* 可见性。对一个volatile变量的读，总是能看到（任意线程对这个volatile变量最后的写入）
* 原子性。对任意单个volatile变量的读/写具有原子性，但类似于volatile++这种复合操作不具有原子性

volatile变量的写-读可以实现线程间的通信。从内存语义的角度来说，volatile的写-读与锁的释放-获取有相同的内存效果：volatile写和锁的释放有相同的语义；volatile读与锁的获取有相同的内存语义

volatile写的内存语义：
* 当写一个volatile变量时，JMM会把该线程对应的本地内存中的共享变量值刷新到主内存，实质上是线程A向某个读取这个变量的线程发出了消息。

volatile读的内存语义：
* 当读一个volatile变量时，JMM会把该线程对应的本地内存置为无效。线程接下来将从主内存中读取共享变量，实质上是线程B读取了某个线程发出的消息。

```java
class VolatileExample{
    int a = 0;
    volatile boolean flag = false;
    
    public void writer(){
        a = 1;//1
        flag = true;//2
    }
    
    public void reader(){
        if (flag){//3
            int i = a;//4
        }
    }
}
```
假设线程A执行writer()方法之后，线程B执行reader()方法。根据happens-before规则，这个过程建立的happens-before关系可以分为两类：
1. 根据程序次序规则，1 happens-before 2; 3 happens-before 4
2. 根据volatile规则，2 happens-before 3
3. 根据happens before的传递性规则，1 happens before 4

![happens_before](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_happens_before.png)

# 锁
```java
class MonitorExample{
    int a = 0;
    
    public synchronized void writer(){//1
        a++;//2
    }//3
    
    public synchronized void reader(){//4
        int i = a;//5
    }//6
}
```
假设线程A执行writer()方法之后，线程B执行reader()方法。根据happens-before规则，这个过程建立的happens-before关系可以分为两类：
1. 根据程序次序规则，1 happens-before 2; 3 happens-before 4; 4 happens-before 5; 5 happens-before 6
2. 根据监视器锁规则，3 happens-before 4
3. 根据happens before的传递性规则，2 happens before 5

![lock](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_lock.png)

锁释放-获取的内存语义与volatile写-读的内存语义相同，锁释放与volatile写有相同的内存语义，锁获取与volatile读有相同的内存语义

* 公平锁和非公平锁释放时，最后都要写一个volatile变量state
* 公平锁获取时，首先会去读这个volatile变量
* 非公平锁获取时，首先会用CAS更新这个volatile变量，这个操作同时具有volatile读和volatile写的内存语义

锁释放-获取的内存语义的实现至少有两种方式：
1. 利用volatile变量的写-读所具有的内存语义
2. 利用CAS所附带的volatile读和volatile写的内存语义

# concurrent包的实现
java线程之间的通信有下面四种方式：
1. A线程写volatile变量，随后B线程读这个volatile变量
2. A线程写volatile变量，随后B线程用CAS更新这个volatile变量
3. A线程用CAS更新一个volatile变量，随后B线程用CAS更新这个volatile变量
4. A线程用CAS更新一个volatile变量，随后B线程读这个volatile变量

通用化的实现模式：
1. 首先，声明共享变量为volatile
2. 然后，使用CAS的原子条件更新来实现线程间的同步
3. 同时，配合以volatile的读/写和CAS所具有的volatile读和写的内存语义来实现线程间的通信

实现示意图
![concurrent_jar](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_concurrent_jar.png)

# final
编译器和处理器的重排序规则
1. 在构造函数内对一个final域的写入，与随后把这个被构造对象的引用赋值给一个引用变量，这个两个操作间不能重排序
2. 初次读一个包含final域的对象的引用，与随后初次读这个final域，这两个操作之间不能重排序


```java
public class FinalExample{
    int i;
    final int j;
    static FinalExample obj;
    
    public FinalExample(){
        i = 1;
        j = 2;
    }
    
    public static void write(){//写线程A执行
        obj = new FinalExample();
    }
    
    public static void reader(){//读线程B执行
        FinalExample object = obj;
        int a = object.i;
        int b = object.j;
    }
}
```
写final域的重排序规则
* JMM禁止编译器把final域的写重排序到构造函数之外
* 编译器会在final域的写之后，构造函数return之前，插入一个StoreStore屏障。这个屏障禁止处理器把final域的写重排序到构造函数之外。

![final_field_1](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_final_field_1.png)

写final域的重排序确保：在对象引用为任意线程可见之前，对象的final域已经被正确初始化过。

读final域的重排序规则
* 在一个线程中，初次读对象引用与初次读改对象包含的final域，JMM禁止处理器重排序这个两个操作（注意，这个规则仅仅针对处理器）。编译器会在读final域操作的前面插入一个LoadLoad屏障。

![final_field_2](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_final_field_2.png)

读final域的重排序确保：在读一个对象的final域之前，一定会先读包含这个final域的对象引用。

final域是引用类型
```java
public class FinalReferenceExample{
    final int[] intArray;
    static FinalReferenceExample obj;
    
    public FinalReferenceExample(){
        intArray = new int[1];//1
        intArray[0] = 1;//2
    }
    
    public static void writerOne(){
        obj = new FinalReferenceExample();//3
    }
    
    public static void writerTwo(){
        obj.intArray[0] = 2;//4
    }
    
    public static void reader(){
        if (obj != null){//5
            int temp1 = obj.intArray[0];//6
        }
    }
}
```

![final_field_3](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_final_field_3.png)

JMM确保线程C至少能看到线程A在构造函数中对final引用对象的成员域的写入。即C至少看到intArray[0]为1。而写线程B对数组元素的写入，读线程C可能看到，也可能看不到。

final引用不能从构造函数内“逸出”
final域的重排序规则需要一个保证：在构造函数内部，不能让这个被构造对象的引用为其他线程可见，也就是对象引用不能在构造函数中“逸出”。
```java
public class FinalReferenceEscapeExample{
    final int i;
    static FinalReferenceEscapeExample obj;
    
    public FinalReferenceEscapeExample(){
        i = 1;//1
        obj = this;//2
    }
    
    public static void writer(){//写线程A执行
        new FinalReferenceEscapeExample();
    }
    
    public static void reader(){//写线程B执行
        if (obj != null){//3
            int temp1 = obj.i;//4
        }
    }
}
```

![final_field_3](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_final_field_3.png)

从上图可以看出：在构造函数返回前，被构造对象的引用不能为其他线程可见，因为此时的final域可能还没有被初始化。在构造函数返回后，任意线程都将保证能看到final域正确初始化之后的值。

# summary
顺序一致性模型是一个理论参考模型，JMM和处理器内存模型在设计上通常会把顺序一致性内存模型作为参考。根据对不同类型读/写操作组合的执行顺序的放松，可以分为下面几种类型：
1. 放松写-读操作顺序，由此产生total store ordering内存模型（TSO）
2. 在前面1的基础上，继续放松程序中写-写操作的顺序，由此产生partial store order内存模型（PSO）
3. 在前面1和2的基础上，继续放松程序中读-写和读-读操作的顺序，由此产生了relaxed memory order内存模型（RMO）和PowerPC内存模型

JMM在不同处理器中需要插入的内存屏障的数量和种类也不相同

![summary_1](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_summary_1.png)

JMM，处理器内存与顺序一致性内存模型之间的关系

![summary_2](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_summary_2.png)

JMM的设计示意图

![summary_3](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_summary_3.png)

JMM把happens-before要求禁止的重排序分成两类：
* 会改变程序执行结果的重排序
* 不会改变程序执行结果的重排序

JMM对两种不同性质的重排序，采取了不同策略：
* 对于会改变程序执行结果的重排序，JMM要求编译器和处理器必须禁止这种重排序
* 对于不会改变程序执行结果的重排序，JMM对编译器和处理器不作要求

![summary_4](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_summary_4.png)

JMM的内存可见性保证
java的内存可见性保证程序类型可以分为三类：
1. 单线程程序。单线程程序不会出现内存可见性问题。编译器，runtime和处理器会共同确保单线程程序的执行结果与该程序在顺序一致性模型中的执行结果相投。
2. 正确同步的多线程程序。正确同步的多线程程序的执行将具有顺序一致性。
3. 未同步/未正确同步的多线程程序。JMM提供最小安全性保障：线程执行时读取到的值，要么是之前某个线程写入的值，要么是默认值（0，null，false）。

![summary_5](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180717/20180717_summary_5.png)

# reference
- [http://ifeve.com/java-memory-model-0/][R1]


[R1]: http://ifeve.com/java-memory-model-0/
