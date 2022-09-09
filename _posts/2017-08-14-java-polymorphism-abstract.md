---
layout: post
title: java抽象类可以使用多态实例化
categories: [java, 多态,polymorphism]
description: java抽象类可以使用多态实例化
keywords: java, polymorphism
---

### 抽象类的实例化

在实例化抽象类，接口时通常使用内部类的方式，可以使用多态的方式实例化抽象方法。
如:
```
Object o1 = new Runnable(){
    @Override 
    public viod run(){};
    }
```

使用多态时父类也是可以使用子类方法的。
#### 使用方法
1. 子类作为方法参数传给父类方法，再用子类调用方法

![](/images/posts/java/superclass-invokes-subclass.png)

2. 子类中用static修饰方法，父类可以用子类调用static修饰的方法

![](/images/posts/java/superclass-invokes-subclass-static-method.png)

3. 父类中用反射获取子类，子类调用方法

![](/images/posts/java/superclass-invokes-subclass-reflection.png)