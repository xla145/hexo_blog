---
title: 如何使用flatMap
date: 2019-03-15 09:52:57
tags:
- JAVA 8
---

# 1. 什么情况下用到flatMap
* 当使用map（）操作时，不是返回一个值，而是返回一个集合或者一个数组的时候，这时候就可以使用flatMap解决这个问题。举个例子，你有一个列表 [21,23,42]，然后你调用getPrimeFactors()方法map操作 使数组转化成stream。
* 上述结果[[3,7],[23],[2,3,7]]，这个类型Stream<String[]> 使用 stream 操作（filter，sum，distinct ...）和 collectors 都不支持这种类型。如果你想把Stream of Stream转换为值列表，使用 flatMap() 方法 重新生成一个Stream对象，最后可以得到 [3,7,2,3,2,3,7]
# 2. flatMap如何工作的
通过下面的图，我们就很容易理解flatmap在java8 中是如何工作的
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190304131022877.png)
使用红色标记的列表和绿色标记的列表，经过flatmap方法后在第二个管道中把把上述两个列表的数据合并成一个列表数据
# 3. flatmap 使用例子
```
		List<List<Integer>> lists = new ArrayList<>();
        List<Integer> list = new ArrayList<>();
        list.add(4444);
        list.add(33333);
        list.add(444444);
        lists.add(list);
        lists.stream().flatMap(Collection::stream).forEach(System.out::println);
```
由于上述 lists.stream() 后返回的是stream<list<Integer>> 所以需要使用flatMap 进行合并
# 3. 总结
本文主要通过为啥使用 flatmap，如何使用flatmap 这两点讲述了flatmap方法，下一章节我们来讲一个map & flatmap。

参考文档
[How to use flatMap() in Java 8](http://www.java67.com/2016/03/how-to-use-flatmap-in-java-8-stream.html)
[Java 8 flatMap示例](https://www.mkyong.com/java8/java-8-flatmap-example/)

