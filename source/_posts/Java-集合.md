---
title: Java 集合
date: 2019-03-15 09:56:13
tags:
- JAVA
---

# 1.	什么叫数据结构？
在计算机科学中，数据结构（英语：data structure）是计算机中存储、组织数据的方式。数据结构意味着接口或封装：一个数据结构可被视为两个函数之间的接口，或者是由数据类型联合组成的存储内容的访问方法封装。
# 2. 结构有哪些？
常见的数据结构主要有8种，引用百度百科的图：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190228100803325.png)
# 3.	一些数据结构在Java中的具体体现？
1.	List 接口
List 具体实现类有ArrayList, LinkedList, Vector，下面我们来介绍一下它们的原理，区别和使用方式
* ArrayList
ArrayList 实现于 List、RandomAccess 接口。可以插入空数据，也支持随机访问。内部实现采用动态数组，当容量不够时，自动扩容至（当前容量1.5倍）。元素的顺序按照插入的顺序排列。默认初始容量为10。随机访问效率高，随机插入、删除效率低。主要的属性是elementData 数组，以及 size 大小。在执行添加add（）方法时
```
	public boolean add(E e) {
	    ensureCapacityInternal(size + 1);  // Increments modCount!!
	    elementData[size++] = e;
	    return true;
	}
```
 主要是做扩容判断，然后再将数据插入到数组中。Add（int index，E data）方法是在特定的位置，插入特定的元素。Index 不大于数组的大小，这种方式添加
 ```
	public void add(int index, E element) {
	    rangeCheckForAdd(index);
	    ensureCapacityInternal(size + 1);  // Increments modCount!!
	    System.arraycopy(elementData, index, elementData, index + 1,
	                     size - index);
	    elementData[index] = element;
	    size++;
	}
```
首先检查index是否超过当前数组的大小，是否扩容判断，数组的复制将需要插入的位置的元素往后移一位，最后执行插入特定元素。
数组的开销主要在于，创建开辟数组空间和数组中的元素移动，所以在平时的操作中，尽量避免使用Add（int index，E data）方法，创建数组前确认数组的长度等。
* 	LinkedList
LinkedList内部使用双向链表实现，随机访问效率低，随机插入、删除效率高。可以当作堆栈、队列、双向队列来使用。LinkedList也是非线程安全的。
链表为啥插入或删除效率高，访问效率低？
主要原因是双链表就是每个node不仅包含指向下后一个结点的引用，还包含着指向前一个结点的引用。由于不必须按顺序存储，链表在插入的时候可以达到O(1)的复杂度，比另一种线性表顺序表快得多，但是查找一个节点或者访问特定编号的节点则需要O(n)的时间，而顺序表相应的时间复杂度O(1)。
链表结构可以克服数组链表需要预先知道数据大小的缺点，链表结构可以充分利用计算机内存空间，实现灵活的内存动态管理。但是链表失去了数组随机读取的优点，同时链表由于增加了结点的指针域，空间开销比较大。接下来单独写一篇关于链表的文章，来具体了解一下链表。
* Vector
Vector跟ArrayList是类似的，内部实现也是动态数组，随机访问效率高。Vector是线程安全的。
由于Vector内部使用了synchronized，所以线程是安全的，但是整体的开销就很大，效率不高。
2.	Set
Set是不能包含重合元素的容器，其实现类有HashSet，继承于它的接口有SortedSet接口等。Set中提供了加、减、和交等集合操作函数。Set不能按照索引随机访问元素，这是它与List的一个重要区别。
讲两个set实现类 hashSet 和 TreeSet
* 	hashSet
HashSet内部是使用HashMap实现的，HashSet的key值是不允许重复的，如果放入的对象是自定义对象，那么最好能够同时重写hashCode与equals函数，这样就能自定义添加的对象在什么样的情况下是一样的，即能保证在业务逻辑下能添加对象到HashSet中，保证业务逻辑的正确性。另外，HashSet里的元素不是按照顺序存储的。HashSet是非线程安全的。
*	TreeSet
一般情况下set是无序的，但是TreeSet存储的元素是按顺序存储的，如果是存储的元素是自定义对象，那么需要实现Comparable接口。TreeSet也是非线程安全的。
3.	Map
Map集合提供了按照“键值对”存储元素的方法，一个键唯一映射一个值。集合中“键值对”整体作为一个实体元素时，类似List集合，但是如果分开来讲，Map是一个两列元素的集合：键是一列，值是一列。与Set集合一样，Map也没有提供随机访问的能力，只能通过键来访问对应的值。
Map的每一个元素都是一个Map.Entry，这个实体的结构是< Key, Value >样式。
* Map总结
HashMap存储键值对。当程序试图将一个key-value对放入 HashMap 中时，程序首先根据该key的hashCode()返回值决定该Entry的存储位置：如果两个Entry的key的hashCode() 返回值相同，那它们的存储位置相同。如果这两个Entry的key通过equals比较返回true，新添加Entry的value将覆盖集合中原有Entry的 value，但key不会覆盖。如果这两个Entry的key通过equals 比较返回false，新添加的Entry将与集合中原有Entry形成Entry 链，而且新添加的 Entry 位于 Entry 链的头部。由于HashMap多线程下扩容会出线程不安全，具体参考[谈谈HashMap线程不安全的体现]( https://blog.csdn.net/qq_24184997/article/details/87979866)这篇文章。
接下来一篇单独讲一下hashMap 在Java8跟之前版本的变化和另一个ConcurrentHashMap 能解决并发情况下的线程不安全
# 4. 总结：
大概讲了一下什么是数据结构，Java集合中一些实现类底层使用的数据结构，并详细介绍了一些Java集合实现类的特点和区别。

参考文档:
[数据结构](https://zh.wikipedia.org/zh-hans/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84)
[JCSprout](https://crossoverjie.top/JCSprout/#/?id=introduction)
