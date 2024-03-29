---
layout: post
title: Java设计模式—模板方法模式(Template Method Pattern)
categories: [设计模式, DesignPattern]
description: 设计模式中英文对照
keywords: 设计模式, DesignPattern 
---


模板方法模式仅仅使用了Java的继承机制，但它是一个应用非常广泛的模式。

1. ### 定义
一个操作中的算法的框架，而将一些步骤延迟到子类中。使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

2. ### 通用类图如下
![](/images/posts/java/AbstractClass.jpg)

Abstract Class叫做抽象模板，它的方法分为两类：模板方法和基本方法。
基本方法：基本方法也叫做基本操作，是由子类实现的方法，并且在模板方法中被调用。由抽象模板提供被调用的方法。
模板方法：可以有一个或者几个，一般是具体的方法，也就是一个框架，实现对基本方法的调度，完成固定的逻辑。

具体模板：ConcreteClass1和ConcreteClass2属于具体模板，实现父类所定义的一个或多个抽象方法，也就是父类定义的基本方法在子类中得以实现

3. ### 通用代码如下

抽象模板类

![抽象模板类](/images/posts/java/2022-09-09-TemplateMethodPattern.png)

具体模板类
![具体模板类](/images/posts/java/2022-09-09-ConcreteClass1.png)

![具体模板类](/images/posts/java/2022-09-09-ConcreteClass2.png)

场景类
![场景类](/images/posts/java/2022-09-09-TemplateMethodPatternTest.png)


4. ### 优点
> 封装不变的部分，扩展可变的部分

> 提取公共部分代码，便于维护

> 行为交由父类控制，子类实现

5. ### 缺点
> 按照我们的设计习惯，抽象类负责声明最抽象、最一般的事物属性和方法，实现类完成具体的事物属性和方法。但是模板方法模式却颠倒了，抽象类定义了部分抽象方法，由子类实现，子类执行的结果影响了父类的结果，也就是子类对父类产生了影响，这在复杂的项目中，会带来代码阅读的难度。


6. ### 总结
> 模板模式就是在模板方法中对基本方法的调用。

7. ## 注意
> 为了防止恶意的操作，一般模板方法前面会加上final关键字，不允许被覆写。

8. # 使用场景
> 封装常用的代码时记得使用，把代码抽取出来作为抽象模板，然后新建类(具体模板)继承抽象模板完成一个功能