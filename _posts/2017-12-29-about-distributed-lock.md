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

# the core of distributed lock
distributed lock的本质是将lock丢到一个third part进行管理, 所有的Client都对third part进行监听. 只有当lock不存在或被释放时, Client才能够获取lock. 同时要注意设置lock的expired time, 防止deadlock发生. 下图是architecture示意图

![architecture](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20171229/20171229_architecture.png)

# three different realizations of distributed lock
distributed lock有三种不同的实现方式, 分别是:
* [基于数据库的optimistic lock][R3]
* [基于redis][R1]
* [基于zookeeper][R2]

# the comparation of three methods
* 从理解的难易程度角度(low to high): 数据库 > redis > Zookeeper
* 从实现的复杂性角度(low to high): Zookeeper >= redis > 数据库
* 从性能角度(high to low): redis > Zookeeper >= 数据库
* 从可靠性角度(high to low): Zookeeper > redis > 数据库

# references
- [http://wudashan.cn/2017/10/23/Redis-Distributed-Lock-Implement/][R1]
- [http://www.dengshenyu.com/java/分布式系统/2017/10/23/zookeeper-distributed-lock.html?utm_source=tuicool&utm_medium=referral][R2]
- [http://blog.csdn.net/lmb55/article/details/78495629][R3]
- [http://blog.csdn.net/erlian1992/article/details/78011313][R4]

[R1]: http://wudashan.cn/2017/10/23/Redis-Distributed-Lock-Implement/
[R2]: http://www.dengshenyu.com/java/%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F/2017/10/23/zookeeper-distributed-lock.html?utm_source=tuicool&utm_medium=referral
[R3]: http://blog.csdn.net/lmb55/article/details/78495629
[R4]: http://blog.csdn.net/erlian1992/article/details/78011313