---
title: 多线程编程的硬件基础与 Java 内存模型
date: 2019-09-08 23:25:54
categories: [开发,多线程]
tags: [Java,多线程]
---

> 本文摘抄自《Java 多线程编程实战指南》核心篇 第十一章小结

**本章介绍了多线程编程的硬件基础以及 Java 内存模型的基础知识。**

---

&emsp;&emsp;高速缓存是一个存取速率远比主内存大而容量远比主内存小的存储部件，其引入弥补了处理器与主内存处理能力之间的鸿沟。**高速缓存相当于一个由硬件实现的散列表，其键为内存地址，其值为从内存读取或者准备写入内存的数据。**高速缓存中的每个桶可包含若干缓存条目。缓存条目中的 Tag 部分包含了内存地址的高位部分比特；Flag 部分指示了缓存条目的有效性；缓存行用于存储从内存读取或者准备写入内存的数据，其容量在 16~256 字节之间不等，一个缓存行可用于存储多个变量。缓存命中意味着待读取或者写入内存的数据在高速缓存中存在相应的副本，这可以提升内存访问效率。缓存未命中包括读未命中和写未命中，它不利于性能，但是由于高速缓存容量的限制又往往是不可避免的。Linux 内核工具 perf 可用来查看缓存未命中情况。现代处理器多采用多级高速缓存，典型的高速缓存层级包括 L1 Cache/L2 Cache 和 L3 Cache。

&emsp;&emsp;缓存一致性协议保障了多个处理器上高速缓存中的数据副本的数据一致性，避免了一个处理器读取到共享变量的旧值以及避免了一个处理器对共享变量所做的更新丢失。MESI 协议是一个广为使用的缓存一致性协议，在该协议下缓存条目的 Flag 可能值包括：M/E/S/I。内存读/写操作是通过处理器发送与接收相关消息并更新缓存条目的 Flag 实现的。这些消息包括：Read/Read Response、Invalidate/Invalidate Acknowledge、Read Invalidate、Writeback。

&emsp;&emsp;写缓冲器与无效化队列的引入弥补了 MESI 协议的性能弱点。

&emsp;&emsp;写缓冲器是处理器内部的一个容量比高速缓存还小的私有高速存储部件。其引入使得内存写操作的执行处理器无须等待其他处理器回复 Invalidate Acknowledge/Read Response 消息便可以执行其他指令，从而减小内存写操作的延迟。写缓冲器能导致写线程对共享变量所做的更新无法被其他处理器同步过去。**存储转发技术使得一个处理器可以直接从写缓冲器中读取该处理器先前执行的写操作的结果，但是它也可能导致可见性问题。**另外，写缓冲器还会导致 StoreLoad 重排序和 StoreStore 重排序。

&emsp;&emsp;无效化队列的引入使得处理器在接收到 Invalidate 消息之后可以立即回复 Invalidate Acknowledge 消息，这减少了发送 Invalidate 消息的处理器的等待时间。无效化队列可能使写线程对共享变量所做的共享无法反映到读线程执行处理器的高速缓存中，即导致可见性问题。无效化队列可以导致 LoadLoad 重排序。

&emsp;&emsp;**从硬件的角度来看，可见性的保障是通过写线程和读线程配对使用存储屏障和加载屏障实现的。**存储屏障能够冲刷写缓冲器使得写线程对共享变量所做的更新能够被其他处理器同步，加载屏障能够清空无效化队列，使得写线程对共享变量所做的更新能够反映在读线程执行处理器的高速缓存之中。

&emsp;&emsp;获取屏障相当于 LoadLoad 屏障和 LoadStore 屏障的组合，释放屏障相当于 StoreStore 屏障和 StoreLoad 屏障的组合。LoadLoad 屏障相当于加载屏障；而 StoreLoad 屏障是“全能型”屏障，它既可以充当存储屏障，也可以充当加载屏障。

&emsp;&emsp;Java 虚拟机（JIT 编译器）为了确保 final 关键字的语义，会在 final 字段初始化与构造器返回之前插入一个 StoreStore 屏障，这使得 final 字段初始化操作无法被重排序到构造器之外，从而确保了构造器返回之后相应对象的 final 字段总是初始化完毕的。**有序性的保障是通过写线程与读线程配对执行释放屏障和获取屏障实现的，同样这些屏障也是 Java 虚拟机（JIT 编译器）替我们的应用程序插入的。**Java 虚拟机（JIT 编译器）会在 volatile 变量写操作之后插入一个 StoreLoad 屏障，该屏障不仅充当了存储屏障以冲刷写缓冲器，它还充当了加载屏障以清空无效化队列从而消除了存储转发技术的副作用。Java 虚拟机（JIT 编译器）会在 volatile 变量读操作前插入一个 LoadLoad 屏障，该屏障充当了加载屏障，用于清空无效化队列。

&emsp;&emsp;Java 内存模型从“什么”（What）的角度来回答线程安全有关问题，JSR 133 对 Java 内存模型进行了增强和修复。**Java 内存模型规定，long/double 型变量以外的任何变量的读/写操作具有原子性；volatile 变量修饰的 long/double 型变量的读/写操作也具有原子性。long/double 型普通变量的读/写操作的原子性取决于具体的 Java 虚拟机。**happens-before 从可见性的角度对有序性进行描述。happens-before 关系具有传递性和累积效果。Java 内存模型定义的 happens-before 规则包括：程序顺序规则/内部锁规则/volatile 变量规则/线程启动规则和线程终止规则。Java 标准库本身也定义了一些 happens-before 规则。从语言的层面来看，这些规则是通过使用 Java 的同步机制实现的；从底层的角度来看，这些规则是由 Java 虚拟机/编译器以及处理器一同协作来落实的，内存屏障则是 Java 虚拟机/编译器和处理器之间的“沟通”纽带。

![本章知识结构图](https://raw.githubusercontent.com/Folgerjun/materials/master/blog/img/Multithreading/Multithreading-Chapter-Eleven.png) 