---
layout: post
title: Java - 死锁示例及解决
categories: [java, Deadlock, Multithreading]
description: Java Tutorial for Language Adavanced - Deadlock Example and Solution
keywords: java, Deadlock, Multithreading
---

# Java 死锁

在 Java 中，死锁（Deadlock）情况是指：两个或两个以上的线程持有不同系统资源的锁，线程彼此都等待获取对方的锁来完成自己的任务，但是没有让出自己持有的锁，线程就会无休止等待下去。线程竞争的资源可以是：锁、网络连接、通知事件，磁盘、带宽，以及一切可以被称作“资源”的东西。

### 示例

![](/images/posts/java/java-deadlock.png)

如上图所示，Thread-1持有资源Object1但是需要资源Object2完成自身任务，同样的，Thread-2持有资源Object2但需要Object1，双方都在等待对方手中的资源但都不释放自己手中的资源，从而进入死锁。

```java

public class DeadLockExample {

    private byte[] lock1 = new byte[0];
    private byte[] lock2 = new byte[0];

    public static void main(String[] args) {
        DeadLockExample deadLockExample = new DeadLockExample();
        Runnable runnableA = new Runnable() {
            @Override
            public void run() {
                synchronized (deadLockExample.lock1) {
                    System.out.printf(
                            "[INFO]: %s get resourceA" + System.lineSeparator(),
                            Thread.currentThread().getName()
                    );
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.printf(
                            "[INFO]: %s trying to get resourceB" + System.lineSeparator(),
                            Thread.currentThread().getName()
                    );
                    synchronized (deadLockExample.lock2) {
                        System.out.printf(
                                "[INFO]: %s get resourceB" + System.lineSeparator(),
                                Thread.currentThread().getName()
                        );
                    }
                    System.out.printf(
                            "[INFO]: %s has done" + System.lineSeparator(),
                            Thread.currentThread().getName()
                    );
                }
            }
        };
        Runnable runnableB = new Runnable() {
            @Override
            public void run() {
                synchronized (deadLockExample.lock2) {
                    System.out.printf(
                            "[INFO]: %s get resourceB" + System.lineSeparator(),
                            Thread.currentThread().getName()
                    );
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.printf(
                            "[INFO]: %s trying to get resourceA" + System.lineSeparator(),
                            Thread.currentThread().getName()
                    );
                    synchronized (deadLockExample.lock1) {
                        System.out.printf(
                                "[INFO]: %s get resourceA" + System.lineSeparator(),
                                Thread.currentThread().getName()
                        );
                    }
                    System.out.printf(
                            "[INFO]: %s has done" + System.lineSeparator(),
                            Thread.currentThread().getName()
                    );
                }
            }
        };
        new Thread(runnableA).start();
        new Thread(runnableB).start();
    }
}
```
> Java 死锁（Deadlock）样例

### Java 死锁侦测

JDK自带了一些简单好用的工具，可以帮助我们检测死锁（如：jstack）。

```
$ jstack $(jps -l | grep 'DeadLockExample' | cut -f1 -d ' ')
```
> 使用jstack侦测目标 JVM 进程

```
...
Java stack information for the threads listed above:
===================================================
"Thread-1":
    at DeadLockExample$2.run(DeadLockExample.java:58)
    - waiting to lock <0x000000076ab660a0> (a java.lang.Object)
    - locked <0x000000076ab660b0> (a java.lang.Object)
    at java.lang.Thread.run(Thread.java:748)
"Thread-0":
    at DeadLockExample$1.run(DeadLockExample.java:28)
    - waiting to lock <0x000000076ab660b0> (a java.lang.Object)
    - locked <0x000000076ab660a0> (a java.lang.Object)
    at java.lang.Thread.run(Thread.java:748)

Found 1 deadlock.
```
> 命令输出

可以看到，jstack已经侦测出 JVM 进程中存在线程死锁的情况，并为我们打印出了相关信息。

JVM 图形化分析工具jconsole也可以帮助我们分析死锁情况。

### Java 死锁预防
1. 确定的顺序获锁

2. 超时放弃

3. 死锁检测