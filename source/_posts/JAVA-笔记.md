---
title: JAVA 笔记
date: 2019-02-19 18:22:46
tags:
- JAVA
---
1. 面向对象和面向过程的主要区别

      面向过程：性能相对较高
      
      面向对象：易扩展性，复用，维护
2. Java的一点特点
    
    跨平台: Java 虚拟机实现平台无关性
    支持多线程，网络编程
3. jvm 和 jre
    
    Jvm：运行 Java 字节码的虚拟机
    Java 是编译与解释共存的语言：机器码的运行效率高于 Java 解释器的
    即时编辑器（JIT）: 优化将字节码转换为机器特定语言的过程相似的字节码
    热点代码: 经常需要被调用的方法和代码块
4. hashCode 和 equals
    
    对象的hashCode 作用：hashCode是一个整型，提高比较效率
5. 线程，程序，进程
    
    程序: 程序是静态的代码
    线程和进程最大的不同在于基本上各进程是独立的，而各线程则不一定，因为同一进程中的线程极有可能会相互影响。
    join() 定义在Thread.java中,作用是等待当前线程死亡，然后执行下一步
    Thread sleep：睡眠，超时后自动回到就绪状态（不会释放对象锁的所有权），等待获取资源cpu
    Object wait（This method should only be called by a thread that is the owner
    * of this object's monitor.）：释放对象锁的所有权，线程处于阻塞状态，等待调用notify/notifyall
    Wait和notify/notifyall 为啥是Object的方法
    wait等待的是对象monitor，由于Java中的每一个对象都有一个内置的monitor对象，自然所有的类都理应有wait/notify方法
    Yield（）
6. 为什么局部变量需要显式设置初始化值 
    
    主要原因是执行顺序，对于局部变量而言，其赋值和取值访问顺序是确定的。Javac 不给局部变量设置初始值，主要原因是一种策略决定，一种规范




