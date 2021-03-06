---
title: jvm 面试问题总结(一)
date: 2019-03-15 09:49:26
tags:
- JVM
---


# 1.java 8 将 jvm 中 永久代去除带来的好处？
* 元空间存放在本地内存中避免之前使用永久代出现内存溢出的问题。
* 类及方法的信息等比较难确定其大小，因此对于永久代的大小指定比较困难，太小容易出现永久代溢出，太大则容易导致老年代溢出。
* 永久代会通过 full GC 进行回收，这种操作复杂度较高，回收效率偏低。
# 2.如何判断对象死亡，有哪两种方法？
1. 引用计数法：
当该对象被引用时，计数器就加1；当引用失效，计数器就减1；任何时候计数器为0的对象就是不可能再被使用的。
缺点：这个方法实现简单，效率高，但是目前主流的虚拟机中并没有选择这个算法来管理内存，其最主要的原因是它很难解决对象之间相互循环引用的问题。 所谓对象之间的相互引用问题，如下面代码所示：除了对象objA 和 objB 相互引用着对方之外，这两个对象之间再无任何引用。但是他们因为互相引用对方，导致它们的引用计数器都不为0，于是引用计数算法无法通知 GC 回收器回收他们。
2. 可达性分析算法：
通过一系列“GC Roots” 的对象为起点，从这些节点开始向下搜索，搜索多走过的路径称为引用链，当对象到GC Roots没有任何的引用链相连就判断该对象已经失效。
# 3.再谈引用：
1. 强引用
	* 概述：强引用在代码中很普遍，平时我们通过new 关键字创建的引用都是强引用，只要强引用存在，垃圾收集器就不会回收引用对象强引用在代码中很普遍，平时我们通过new 关键字创建的引用都是强引用，只要强引用存在，垃圾收集器就不会回收引用对象
	* 实现：通过new 关键字创建的引用
2. 软引用
	* 概述：描述一些还有用但是并不必需的对象，如果内存空间足够，垃圾回收器就不会回收它，如果发生内存溢出之前，垃圾回收器将这些对象列进回收范围之中进行第二次回收。如果这次回收还是没有足够的内存，就会发生内存溢出。
	* 用途：软引用可用来实现内存敏感的高速缓存。
	* 实现：Jdk 提供 SoftReference 类来实现软引用。
3. 弱引用
   * 概述：非必要对象，当GC发生就会被回收，生命周期很短暂
   * 实现：Jdk 提供 WeakReference 类来实现弱引用。
   **注意：由于垃圾回收器是一个优先级很低的线程， 因此不一定会很快发现那些只具有弱引用的对象**
4. 虚引用
	* 概述：与其他几种引用都不同，虚引用完全不会对其生存时间构成影响，也无法通过虚引用来取得一个对象实例。
	* 用途：使对象被收集器回收时收到一个系统通知。
	* 实现：Jdk 提供 PhantomkReference 类来实现虚引用。
5. 弱引用和软引用的区别
只具有弱引用的对象拥有更短暂的生命周期。在垃圾回收器线程扫描它 所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收掉只被弱引用关联的对象。
# 4.HotSpot为什么要分为新生代和老年代？
一般将java堆分为新生代和老年代，这样我们就可以根据各个年代的特点选择合适的垃圾收集算法。
* 新生代：每次收集都会有大量对象死去，所以可以选择复制算法
* 老年代：对象存活几率是比较高的，而且没有额外的空间对它进行分配担保，所以我们必须选择“标记-清除”或“标记-整理”算法进行垃圾收集
# 5.Minor GC和Full GC 分别指什么？
* 新生代（Minor GC）:指新生代的垃圾收集，执行操作频繁，回收速度较快。
* 老年代（Major GC/Full GC）:指发生老年代的垃圾回收，出现Major GC经常会伴随一次Minor GC （并非绝对，只有当发生Major GC时 晋升到老年代的内存 大于老年代的剩余内存，这种情况下 会发生Full GC），Major GC的速度一般会比Minor GC的慢10倍以上。
默认情况下 新生代和老年代的堆空间的分布情况
![在这里插入图片描述](https://img.mupaie.com/20190228151004551.png)
注意：虚拟机每次只会使用 Eden 和其中的一块 Survivor 区域来为对象服务，所以无论什么时候，总是有一块 Survivor 区域是空闲着的。 因此，新生代实际可用的内存空间为90%的新生代空间。
大数据（数组和字符串【字符数组】 ）直接被存放在老年代中，原因是（为了避免为大对象分配内存时由于分配担保机制带来的复制而降低效率）
* 长期存活的对象进入老年代
每次进行垃圾回收存活下来的对象年龄都会加一岁，等达到一定程度后该对象就会进行老年代。具体设置年龄阈值，通过参数 -XX:MaxTenuringThreshold 来设置。
* 动态对象年龄判定
有一种情况当相同年龄的对象总和大于Survivor 空间的一半时，年龄大于或等于该值的对象直接进入老年代
# 6. 垃圾回收算法有哪些？优缺点？
* 标记 - 清除算法
标记需要回收的对象，标记完成后在垃圾回收器统一收集那些被标记的对象。
缺点：
  * 效率不高：标记和清除过程效率不高，主要原因是标记的对象分布不均匀
  * 空间问题：清除后产生大量内存碎片，导致以后程序不能给大对象分配连续的内存空间
* 复制算法
将内存分成两块容量大小相同区域，当一块区域内存用完后，经过GC之后幸存的对象将复制到另外一个区域中，剩余的内存空间一次清除即可，不管什么时刻总有一块区域是空闲的。
	* 效率高：每次只需要回收复制后剩余的内存，这样也不会产生内存碎片的问题，移动对顶指针，按顺序分配内存即可，实现简单，运行高效，但是如果存活率较高的情况，复制算法的效率也会随着下降
	* 成本高：时刻保证有一块区域是空闲的，这样导致新生代有10%的内存是浪费的。由于 Eden : from : to = 8:1:1
	![在这里插入图片描述](https://img.mupaie.com/20190307141705245.png)
* 标记-整理算法
跟上述说的标记-清除算法很类似，但是后续步骤不是对可回收的对象直接回收，而是让这一部分标记的对象都向一端移动，最终清除靠近端边的内存，这样好处减少内存碎片的产生。
缺点：实现起来复杂，执行步骤较多
![在这里插入图片描述](https://img.mupaie.com/20190307142226586.png)
* 分代收集算法
目前很多虚拟机的垃圾回收器都采用分代收集算法，主要原因分代回收能根据对象的生存周期进行内存划分，主要分新生代和老年代并根据每个年代的特点采用不同的回收算法，使内存回收更加高效。由于新生代，每次垃圾回收都有大片对象死亡，只有少数对象存活这样我们使用复制算法。老年代每次垃圾回收只有少量对象死亡，整体存活率较高，没有额外的内存对它进行分配，所以采用标记-整理算法或标记-清除算法
# 7.垃圾回收器有哪些？
* Serial 收集器
单线程进行垃圾回收工作，在收集过程中，必须停止其他所有的工作线程，直到回收结束其他工作线程才能继续工作。
算法：复制算法
* ParNew 收集器
对上述收集器进行补充，采用多线程对垃圾进行回收，这样相比单线程整体的效率得到提高。
算法：复制算法
* Parallel Scavenge 收集器
和上述收集器不同的是，Parallel Scavenge 关注点主要是达到一个可控制的吞吐量。所谓的吞吐量就是CPU 用于运行代码的时间与CPU总消耗时间的比值，吞吐量 = 运行代码的时间/(运行代码的时间 +垃圾回收的时间)，吞吐量越高，则相对的垃圾回收时间降低。那平时我们如何控制吞吐量呢，Parallel Scavenge给了我们两个参数来控制吞吐量 最大垃圾收集停顿时间 -XX：MaxGCPauseMillis 和 设置吞吐量大小 -XX:GCTimeRatio 参数，具体参考 [深入理解Java虚拟器]()
算法：复制算法
* Serial old 收集器
主要用于老年代的垃圾回收，采用单线程进行垃圾回收。
算法：标记-整理
* Parallel old 收集器
主要用于老年代的垃圾回收，采用多线程进行垃圾回收。和 Parallel Scavenge 收集器一样， Parallel old 关注点也是吞吐量
算法：标记-整理
* cms 收集器
CMS 收集器是一种以获取最短回收停顿时间为目标的收集器，基于标记-清除算法实现，具体包括四个步骤：
	* 初始化标记
	* 并发标记
	* 重新标记
	* 并发清除
整体执行过程如下图所示：
![在这里插入图片描述](https://img.mupaie.com/20190307151001702.png)
从上图可以看出，除了初始标记和重新标记需要“Stop The World”,其余都是可以和用户线程一起执行的。
缺点：
   * 对CPU 资源非常敏感
   * 无法处理浮动垃圾，由于清除过程伴随着用户线程，用户线程产生的新垃圾在标记过后，CMS 无法在当次收集中处理它们，留给下次GC再进行处理，这部分垃圾就叫做浮动垃圾
   * 由于采用 标记-清除算法会产生大量内存碎片
* G1 收集器
G1 是当今收集器技术中的最前沿技术，相比其他收集器它具有几个特点：
	* 并行与并发
	* 分代收集
	* 空间整理
	* 可预测的停顿

算法：复制算法和标记-整理算法


参考文档
	[深入理解Java虚拟器]()
	[Java面试通关手册](https://github.com/Snailclimb/JavaGuide/blob/master/Java%E7%9B%B8%E5%85%B3/Java%E8%99%9A%E6%8B%9F%E6%9C%BA%EF%BC%88jvm%EF%BC%89.md)

