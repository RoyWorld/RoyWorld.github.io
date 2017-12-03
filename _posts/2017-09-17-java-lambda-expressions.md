---
layout: post
title:  "java lambda expressions"
date:   2017-09-17 23:49:20 -0700
category: java
published: true
author: RoyChan
tags:
- java
- lambda expressions
---

# change of interface
在java8中，interface可加入一个由default关键字修饰的method，这个method类似于abstract method，有具体的实现方式，但却不需要subclass继承

<java>

    interface Formula {
        double calculate(int a);
    
        default double sqrt(int a) {
            return Math.sqrt(a);
        }
    }
    
    Formula formula = new Formula() {
        @Override
        public double calculate(int a) {
            return sqrt(a * 100);
        }
    };
    
    formula.calculate(100);     // 100.0
    formula.sqrt(16);           // 4.0
    
</java>

# why we need lambda expressions
lambda expressions的出现最主要的目的就是为了简化anonymous class的书写
，上述代码可做如下改写
<java>

    Formula formula1 = a -> sqrt(a * 100);
    
</java>
however，如果真的在compiler上做这样的改写，你会发现这会出现compile error<code>:stuck_out_tongue_winking_eye:</code>，实际上这里涉及到一个lambda的scope问题，这在后面会有详细地讲解

# FunctionalInterface annotation
<code>@FunctionalInterface</code>这个注解是解决interface中type inference问题。当interface中含有generic type时，需要加入此注解来让compiler识别出确定的type，如以下代码。

<java>
    
    @FunctionalInterface
    interface Converter<F, T> {
        T convert(F from);
    }
    
    Converter<String, Integer> converter = (from) -> Integer.valueOf(from);
    Integer converted = converter.convert("123");
    System.out.println(converted);    // 123
    
</java>

# lambda in constructor and method
constructor和method的lambda expressions是使用运算符<code>::</code>。这本质上就是用constructor或method来替换对应在interface中declare的method。因为是替换，所以parameter type和return type都必须一致。而对于method来说，需要使用static来修饰，否则需要相关的object。
<java>

    //constructor相关
    class Person {
        String firstName;
        String lastName;
    
        Person() {}
    
        Person(String firstName, String lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }
    }
    
    interface PersonFactory<P extends Person> {
        P create(String firstName, String lastName);
    }
    
    PersonFactory<Person> personFactory = Person::new;//这里使用的是第二个constructor
    Person person = personFactory.create("Peter", "Parker");
    
</java>
<java>
    
    //method相关
    class Something {
        String startsWith(String s) {
            return String.valueOf(s.charAt(0));
        }
    }
    
    Something something = new Something();
    Converter<String, String> converter = something::startsWith;//没用static修饰
    String converted = converter.convert("Java");
    System.out.println(converted);    // "J"

</java>

# lambda scopes
这里借[官方文档](http://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.27.2)的一段话:

>Unlike code appearing in anonymous class declarations, the meaning of **names** and the **this** and **super** keywords appearing in a lambda body, along with the accessibility of referenced declarations, are the same as in the surrounding context (except that lambda parameters introduce new names).

大意就是关键字names、this和super，以及the accessibility of referenced declarations都与周围的上下文一样，也就是所有variable和method的reference都是与周围上下文一样的，新的variable和method需要做重新的declare。下面一段摘抄自StackOverflow中的code，可以很好地表达这其中的含义。

<java>

    class Main {
    
        public static void main(String[] args) {
            new Main().foo();
        }
    
        void foo() {
            System.out.println(this);
            Runnable r = () -> {
                System.out.println(this);
            };
            r.run();
        }
    }

</java>
输出的结果:

<java>

    Main@f6f4d33
    Main@f6f4d33
    
</java>

对于local variables，可以不使用final来做修饰，但必须是implicitly final，否则会有compile error。而对于field和static variables，都是和anonymous class一致，这里就不再赘述了


# references:
- http://winterbe.com/posts/2014/03/16/java-8-tutorial/
- https://stackoverflow.com/questions/33108540/why-can-we-not-use-default-methods-in-lambda-expressions
- http://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.27.2

