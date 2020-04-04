---
title: 关于Volatile的那点事
date: 2019-03-15 09:53:46
tags:
- 多线程
- 并发
---
# 1.	为啥要使用Volatile？
通常我们在Java中给变量加上Volatile修饰，主要用途是使该变量具有可见性。因为在多线程的操作中，由于每个线程内部都会开辟一个内存空间，如果想让每个空间都可见该变量则需要给这个变量加在Volatile修饰，使变量变成共享变量。它也能避免 jvm 指令重排。
# 2. Volatile 实现原理？
在了解volatile原理之前，我们先了解一下cpu和主存，关于cpu的定义如下图：
![cpu术语定义](https://img.mupaie.com/20190301100135878.png)
知道cpu定义后，我们来看一下内存和cpu是怎么交互的，由于内存处理速度和cpu的处理速度差距太大，为了平衡两者之间的差距引入了cpu
缓存如下图：
![cpu和内存的交互](https://img.mupaie.com/20190301101210752.png)
知道内存和cpu的交互后，我们来解决一个问题 -- volatile 关键字是如何实现变量可见性的呢，我们通过一段代码来展示如何实现可见性的：
Java 代码如下：
object = new Singleton(); // object 是 volatile 修饰的变量
转变成汇编代码：
 ![在这里插入图片描述](https://img.mupaie.com/20190301100533374.png)
 我们发现通过volatile 修饰的变量进行写操作的时候会多出一段Lock 前缀指令，这段指令的用途主要有两点：
 1. 将当前的处理器缓存行写会系统内存
 2. 这个写回操作使其他cpu里面缓存该值的内存地址数据实效。
我们来了解一下实现过程，Lock前缀指令在执行期间会给处理器发送一个LOCK#信号，处理器收到信号后会锁住cpu缓存并将变量写入主内存中。由于处理器使用嗅探技术确保它内存缓存，系统内存和其他处理器的缓存的数据在总线上保持一致。如果嗅探到有一个处理器来检测其他处理器打算写入内存地址，而这个地址是处于共享状态的，那么正在嗅探的处理器中缓存的内存地址将变成无效，下次访问的时候直接读取主存中的内存地址。
# 3. volatile 能实现可见性，但不能保证原子性
虽然volatile关键字能保证从主内存中直接获取被volatile修饰的变量，也能直接把volatile修饰的变量写入主内存中，但是这样也不能保证原子性。在多cpu的情况下，还是会出现线程不安全情况，比如：在多线程下，多个线程正在写入一个volatile变量，并正确的将值写入主内存中，如果新写入的值不是根据之前的值计算得到的，那么就不能保证数据原子性了。
除了上述写了还有一种情况：
在多线程中，递增一个volatile变量counter，通过volatile不足够解决问题，下面我们通过一个例子来解释：
你想象一下如果线程1读取值为0的变量counter将他写入cpu缓存中，递增这个变量counter将它变成1，没有将修改的值写入主内存中。线程2也能从主内存中读取到相同的变量counter，但是这个变量counter还是0，然后将变量counter写入cpu的缓存中，线程2也递增了这个变量counter为1，没有将值写入主内存中，这种情况如下图所示：
![在这里插入图片描述](https://img.mupaie.com/20190301144702874.png)
线程1和线程2现在不是同步的。上述结果原本应该是2，但是因为两个线程都没有把变量counter写入主内存，导致主内存的变量counter还是0，即使最终线程1和2将变量counter写入主内存也是错误的。
# 4. 怎么保证volatile原子性呢？
如果两个以上的线程对共享变量读写操作，使用volatile并不能解决问题。你需要使用 synchronized 来确保对变量读写操作使原子性的。不止可以通过synchronized锁解决这个问题，你也可以使用 java.util.concurrent package 下的一些原子数据，比如 AtomicLong，AtomicReference 还有其他一些。volatile关键字能保证在32和64位的变量运行。

# 5. volatile 性能考虑
读取和写入volatile变量会导致变量被读取或写入主存储器。读取和写入主内存比访问CPU缓存更昂贵。访问volatile变量也会阻止指令重新排序，这是一种正常的性能增强技术。因此，当您确实需要强制实施变量可见性时，应该只使用volatile变量。

# 6. 总结
主要讲了一些 volatile 的底层实现和如何保证volatile原子性，最后讲了一下 需要考虑一些 volatile 性能。
注意：__使用 volatile 修饰的变量避免该变量计算需要依赖之前的值。__

参考文档：
[Java Volatile Keyword](http://tutorials.jenkov.com/java-concurrency/volatile.html)
[聊聊并发（一）——深入分析 Volatile 的实现原理](https://www.infoq.cn/article/ftf-java-volatile)
