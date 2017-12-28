---
layout: post
title:  "About Distributed Lock"
date:   2017-12-29 09:37:31
category: architecture
published: true
author: RoyChan
tags:
- java
- ditributed
- concurrent
---

# the conditions of distributed lock
distributed lock要满足的四个条件: 
* 互斥性。在任意时刻，只有一个客户端能持有锁。
* 不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。
* 具有容错性。只要大部分的Redis节点正常运行，客户端就可以加锁和解锁。
* 解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了。

# three different realizations of distributed lock
distributed lock有三种不同的实现方式, 分别是:
* 数据库的乐观锁
* 基于redis
* 基于zookeeper

# the core of distributed lock
distributed lock的本质是

`@Target`表示该注解用于什么地方
* `ElemenetType.CONSTRUCTOR` 构造器声明 
* `ElemenetType.FIELD` 域声明（包括enum实例） 
* `ElemenetType.LOCAL_VARIABLE` 局部变量声明 
* `ElemenetType.METHOD` 方法声明 
* `ElemenetType.PACKAGE` 包声明 
* `ElemenetType.PARAMETER` 参数声明 
* `ElemenetType.TYPE` 类, 接口（包括注解类型）或enum声明 

`@Retention`表示在什么级别保存该注解信息. 可选的 RetentionPolicy 参数包括: 
* `RetentionPolicy.SOURCE` 注解将被编译器丢弃 
* `RetentionPolicy.CLASS` 注解在class文件中可用, 但会被VM丢弃 
* `RetentionPolicy.RUNTIME VM` 将在运行期也保留注释, 因此可以通过反射机制读取注解的信息. 

`@Documented`用于描述其它类型的annotation应该被作为被标注的程序成员的公共API, 因此可以被例如javadoc此类的工具文档化

`@Inherited`元注解是一个标记注解, `@Inherited`阐述了某个被标注的类型是被继承的


# annotion resolver



# about resolve and application fo self-defined annotion 


# references
- [http://wudashan.cn/2017/10/23/Redis-Distributed-Lock-Implement/][R1]
- [http://www.dengshenyu.com/java/%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F/2017/10/23/zookeeper-distributed-lock.html?utm_source=tuicool&utm_medium=referral][R2]

[R1]: http://wudashan.cn/2017/10/23/Redis-Distributed-Lock-Implement/
[R2]: http://www.dengshenyu.com/java/%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F/2017/10/23/zookeeper-distributed-lock.html?utm_source=tuicool&utm_medium=referral