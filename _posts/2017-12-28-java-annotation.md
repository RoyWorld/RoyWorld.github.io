---
layout: post
title:  "java annotation"
date:   2017-12-28 17:43:02
category: java
published: true
author: RoyChan
tags:
- java
- annotation
---

## Annotation

# Annotation分类
根据注解参数的个数, 分为三类:
* 标记注解:一个没有成员定义的Annotation类型被称为标记注解. 这种Annotation类型仅使用自身的存在与否来为我们提供信息. 比如后面的系统注解`@Override`
* 单值注解
* 完整注解　

根据注解使用方法和用途, 分为三类: 
* JDK内置系统注解
* 元注解
* 自定义注解

# JDK内置系统注解
* `@Override`: 用于修饰此方法覆盖了父类的方法
* `@Deprecated`: 用于修饰已经过时的方法
* `@SuppressWarnnings`: 用于通知java编译器禁止特定的编译警告

# 元注解
元注解的作用就是负责注解其他注解. Java5.0定义了4个标准的meta-annotation类型, 它们被用来提供对其它annotation类型作说明

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

# 自定义注解

### 定义注解格式
`public @interface 注解名 {定义体}`

其中的每一个方法实际上是声明了一个配置参数. 方法的名称就是参数的名称, 返回值类型就是参数的类型（返回值类型只能是基本类型、Class、String、enum）. 可以通过default来声明参数的默认值. 

注: 使用`@interface`自定义注解时, 自动继承了`java.lang.annotation.Annotation`接口, 由编译程序自动完成其他细节. 在定义注解时, 不能继承其他的注解或接口

### 可支持的数据类型
* 所有基本数据类型（int,float,boolean,byte,double,char,long,short)
* String类型
* Class类型
* enum类型
* Annotation类型
* 以上所有类型的数组

-- 例子

# 注解处理器
当实现一个注解, 若没有与之相关的处理器, 那么该注解就无异于注释, 而注解的处理就需要用到反射机制中的`AnnotatedElement`接口

### 实现AnnotatedElement接口的类
`AnnotatedElement`接口代表程序中可以接受注解的程序元素,该接口主要有如下几个实现类: 
* Class: 类定义
* Constructor: 构造器定义
* Field: 累的成员变量定义
* Method: 类的方法定义
* Package: 类的包定义

### AnnotatedElement接口的方法
* 方法1: `<T extends Annotation> T getAnnotation(Class<T> annotationClass)`: 返回改程序元素上存在的、指定类型的注解, 如果该类型注解不存在, 则返回null. 
* 方法2: `Annotation[] getAnnotations()`:返回该程序元素上存在的所有注解. 
* 方法3: `boolean is AnnotationPresent(Class<?extends Annotation> annotationClass)`:判断该程序元素上是否包含指定类型的注解, 存在则返回true, 否则返回false.
* 方法4: `Annotation[] getDeclaredAnnotations()`: 返回直接存在于此元素上的所有注释. 与此接口中的其他方法不同, 该方法将忽略继承的注释. （如果没有注释直接存在于此元素上, 则返回长度为零的一个数组. ）该方法的调用者可以随意修改返回的数组；这不会对其他调用者返回的数组产生任何影响. 

# 对于自定义注解处理和用途
1. 对于每个URI进行权限控制, 在controller的每个request上加入这种注解, 写一个自定义拦截器, 在请求处理前进行权限的判断

2. 结合spring aop的特性, 在service执行某个方法前后去执行自定义注解的相关方法

以上两者是从两个不同的方面进行运用, 一个是spring mvc, 另一个是spring data

# references
- http://www.cnblogs.com/peida/archive/2013/04/23/3036035.html
- https://www.cnblogs.com/acm-bingzi/p/javaAnnotation.html