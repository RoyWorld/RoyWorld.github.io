---
layout: post
title:  "The Core Of Algorithm"
date:   2018-02-26 11:24:45
category: algorithm
published: true
author: RoyChan
tags:
- java
- algorithm
---

# what is algorithm
In my opinion, 算法实质就是描述某种抽象问题的解决方案. However, 这仅仅只是对那些比较高级和著名的算法而言, 如: DFS和BFS解决了如何遍历一个图，Shortest Path解决了节点间的最短路径问题. 

而对于一般的算法, 它们应用的范围并不广泛, 用途仅仅只是解决某个特定问题, 如: 解决某一算法题所用到的算法. 这类算法通常是由最基本的操作所组成的,  

# algorithm and math
algorithm和math的联系是非常紧密的, 而algorithm对应的数学分支是Concrete Mathematics. why? 下面用一个例子说明.

首先要明确的是，Concrete Mathematics是由**集合论、图论、代数结构、组合数学和数理逻辑**组成, 其中最为重要的是**图论、代数结构和数理逻辑**. 这三部分的知识是用得最多的, 下面也是围绕着这三部分进行讲解，而这三部分的相关知识这里就不再赘述了

对于一个实际的问题，通过合理地建模, 便可运用相关知识进行分析，以下是建模的方式:
* 图论, 将现实中的结构化模型抽象成由点线组成的图
* 代数结构, 将输入看作是数据集，而运算的过程中所涉及的所有操作看作是操作集
* 数理逻辑, 将整个运算过程转化成逻辑推理过程



# basic operators
所有的algorithm都包含以下这些基本的操作, 这些操作在某种程度上是等同于基本运算法则, 如: 加减乘除、异或、同或. 而算法中最为常用的基本操作如下:
* 遍历(traverse)

```java
/**
 * Pseudo code
 */
for i = 1:n
    ...
end
```
* 交换(swap)

```java
/**
 * Pseudo code
 */
swap(a, b)
```
* 比较(comparation)

```java
/**
 * Pseudo code
 */
compare(a, b)
```
* 递归(recursion)

```java
/**
 * 写recursion的关键在于确定好边界条件
 * 也就是退出递归的方式
 * 不然会抛出SOE异常
 * 
 * 下面以Fibonacci数列为例
 * f(1) = 1
 * f(2) = 2
 * f(n+2) = f(n) + f(n+1)
 * Pseudo code
 */
int f(n){
    if(n == 1) return 1;
    else if(n == 2) return 2;
    else return f(n-1) + f(n-2);
}
```

下面以ISort为例, 近距离的观察算法是怎么运用到basic operators
ISort的核心思想是**假设序列中从0到k的元素是已排序的**, 操作上通过将第k+1个元素与前K元素进行比较, 来确定第k+1个元素的在前k+1个元素的位置. 

ISort的伪代码如下:
```java
for i = 2:n,
    for (k = i; k > 1 and a[k] < a[k-1]; k--)
        swap a[k,k-1]
    → invariant: a[1..i] is sorted
end
```
下面分别用traverse和recursion来实现ISort
```java
/**
 * traverse版本
 */
void iSort(Integer[] a){
    for (int i = 2; i < a.length; i++) {
        for (int j = i; j > 0 && a[j] < a[j-1]; j--) {
            a[j-1] = swap(a[j], a[j] = a[j-1]);
        }
    }
}
```
```java
/**
 * recursion版本
 */
void iSort2(Integer[] a, int index){
    index++;
    for (int j = index; j > 0 && a[j] < a[j-1]; j--) {
        a[j-1] = swap(a[j], a[j] = a[j-1]);
    }
    if (index == a.length-1){
        return;
    }else {
        iSort2(a, index);
    }
}
```

# important thought
* 贪心算法(gready algorithms)
* 分治策略(divide and conquer strategy)
* 动态规划(dynamic programming)

