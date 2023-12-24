---
title: 走近 Java 世界中的线程
date: 2019-06-15 13:47:54
categories: [开发,多线程]
tags: [Java,多线程]
---

> 本文摘抄自《Java 多线程编程实战指南》核心篇 第一章小结

**本章介绍了线程、多线程编程这两个基本概念以及 Java 平台对线程的实现。**

---

- 进程是程序的运行实例，一个进程可以包含多个线程，这些线程共享其所在进程的资源。

- **线程是进程中可独立执行的最小单位。**Java 标准库类 `java.lang.Thread` 就是 Java 平台对线程的实现。特定线程总是在执行特定的任务，线程的 `run` 方法就是线程所要执行任务的处理逻辑的入口方法，该方法由 Java 虚拟机直接调用执行。Java 标准库接口 `java.lang.Runnable` 就是对任务的抽象，**Thread 类就是 Runnable 接口的一个实现类**。

- **应用程序负责线程的创建与启动，而线程调度器负责线程的调度和执行。**Java 平台中有两种方式创建线程：创建 Thread 的子类和以 Runnable 接口实例为构造器参数直接通过 new 创建 Thread 实例。

- 在 Java 平台中，任何一段代码总是执行在确定的代码中的。同一段代码可以被不同的线程执行。代码可以通过 `Thread.currentThread()` 调用来获取其当前执行线程。

- 为每个线程设置一个简短而含义明确的名称属性有助于多线程程序的调试和问题定位。

- 一个线程从其创建到运行结束的整个生命周期会经历若干状态。线程执行过程中调用一些对象的方法（如 `Thread.sleep(long millis)`）或者执行特定的操作（如 I/O 操作）往往导致其状态的变更。线程转储是对线程进行监视的重要媒介。操作系统以及 JDK 都提供了一些工具（jvisualvm、jstack 和 Java Mission Control），可以用来获取线程转储。

- Java 平台是一个多线程的平台，线程的身影在 Java 平台中无处不在。按照线程间的创建关系，我们可以将多个线程间的关系理解为一个层次关系。**Java 并无相关 API 用于获取一个线程的父线程或子线程，父线程和子线程之间的生命周期并无必然联系。**

- **线程是多线程编程的基本单位。多线程编程一方面有助于提高系统的吞吐率、提高软件的响应性、充分利用多核处理器资源、最小化对系统资源的使用和简化程序的结构，另一方面面临线程安全问题、线程活性问题、上下文切换和可靠性等问题。**因此，多线程编程绝不仅仅是使用多个线程进行编程那么简单，多线程编程有其自身需要解决的问题，而这正是后续章节的主要内容。

![本章知识结构图](https://raw.githubusercontent.com/Folgerjun/materials/master/blog/img/Multithreading/Multithreading-Chapter-One.png)