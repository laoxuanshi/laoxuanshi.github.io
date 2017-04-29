---
layout: post
title: Java并发之synchronized关键字
categories: [Java, Multithreading]
description: synchronized in java
keywords: Java, Multithreading, synchronized
---

多个线程共享某段内存数据时可能会出现线程安全问题。利用synchronized关键字对数据加锁可以保证线程安全问题。

# synchronized使用

## 特点

- 假如线程A持有某对象的锁，那线程B异步调用**非**synchronized类型的方法不受限制。
- 一个类的对象锁和另一个类的对象锁是没有关联的，当一个线程获得A类的对象锁时，它同时也可以获得B类的对象锁。
- synchronized锁是可重入的。
  - 一个线程得到某个对象锁后，再次请求此对象锁时是可以得到该对象的锁的。
  - 子类可通过“可重入锁”调用父类的同步方法。
- 同步不具有继承性。
  - 还得在子类的方法中添加synchronized关键字

## 什么是多线程安全？

- 线程安全：经常用来描绘一段代码。指在并发的情况之下，该代码经过多线程使用，线程的调度顺序不影响任何结果。例如采用了加锁机制，当一个线程访问该类的某个数据时，进行保护，其他线程不能进行访问直到该线程读取完，其他线程才可使用。不会出现数据不一致或者数据污染。这个时候使用多线程，我们只需要关注系统的内存，cpu是不是够用即可。
- 线程不安全就是不提供数据访问保护，多线程先后更改数据会产生数据不一致或者数据污染的情况，意味着**线程的调度顺序会影响最终结果**。
- 一般来说，只有被传入多个Thread的Runnable对象的实例变量可能出现“非线程安全”问题，方法内的局部变量不会。

## 什么是临界区？

被synchronized加锁的代码称为“临界区”

- 临界区是访问一个共享资源在同一时间不能被超过一个线程执行的代码块。 同步机制就是Java语言帮助程序员实现临界区。

## 同步方法和同步代码块有什么区别？

- 同步方法：利用synchronized关键字修饰的方法。

  - 由于java的每个对象都有一个内置锁，当用此关键字修饰非静态方法时，内置锁会保护整个方法。在调用该方法前，需要获得内置锁，否则就处于BLOCKED状态。
  - synchronized关键字也可以修饰静态方法，此时如果调用该静态方法，将会锁住整个类。静态方法则一定会同步，非静态方法需在单例模式才生效，推荐用静态方法。

  ```java
  public synchronized void example(int n);
  public static synchronized void example(int n);
  ```

- 同步代码块：利用synchronized关键字修饰的语句块。被该关键字修饰的语句块会自动被加上锁，从而实现同步。

  - synchronized(非this对象x)，将x对象作为“对象监视器”

  ```java
  //对象内置锁
  synchronized (this) {   
    //code block   
  }
  //将other对象作为对象锁
  synchronized (other) {   
    //code block   
  }
  //锁住整个类
  synchronized (Example.class) {   
    //code block   
  }
  ```

- 同步是一种高开销的操作，因此应该尽量减少同步的代码部分。如果没有必要同步整个方法，使用synchronized代码块同步关键代码即可。

## 对象锁和类锁有什么区别？

- 类锁和和对象锁是两个不一样的锁，控制着不同的区域，它们是互不干扰的。
- 线程获得对象锁的同时，也可以获得该类锁，即同时获得两个锁，这是允许的。

## 锁什么时候释放？

- 同步代码块执行完毕
- 执行同步代码块时出现异常
- 同步代码块中执行锁所属对象的wait方法

# synchronized底层实现

本质上都是对指定对象相关联的monitor的获取，具有互斥性

- 同步代码块使用了 **monitorenter** 和 **monitorexit** 指令实现。
- 同步方法中依靠方法修饰符上的 **ACC_SYNCHRONIZED** 实现。

monitor是线程私有的数据结构，每一个线程都有一个可用monitor列表，同时还有一个全局的可用列表，先来看monitor的内部。

![](/images/posts/Multithreading/synchronized-monitor.png)