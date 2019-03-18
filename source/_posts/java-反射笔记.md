---
title: java 反射笔记
date: 2019-02-20 18:39:41
tags:
- JAVA
- 反射机制
---


# 1. 什么叫反射？

反射机制，在Java程序运行过程中动态获取类信息（变量，方法等），以及动态调用对象方法的功能，重点是在**程序运行中**

官方对反射的解释：

通过反射，我们可以在运行时获得程序或程序集中每一个类型的成员和成员的信息。程序中一般的对象的类型都是在编译期就确定下来的，而 Java 反射机制可以动态地创建对象并调用其属性，这样的对象的类型在编译期是未知的。所以我们可以通过反射机制直接创建对象，即使这个对象的类型在编译期是未知的。

# 2. 反射机制的用途

* l在运行时分析类的能力。

* l在运行时查看对象。

* l实现通用的数组操作代码。

* l利用 Method 对象， 这个对象很像中的函数指针。

很多框架中使用了反射机制，最经常用的Spring IOC 创建bean的过程就是通过反射实现的。

# 3. 反射的运用

Java反射主要是获取一个Class 对象（在程序运行期间，Java 运行时系统始终为所有的对象维护一个被称为运行时的类型标识）获取Class 有三种方式

1：使用Class的类方法 forName

Class.forName("类路径")

平时我们在加载jdbc中使用Class.forName(driver)，加载数据库驱动

2：直接对象的class

Class cl = String.class;

3：调用对象的getClass()方法

String s = "uuu";  
Class cl = s.getClass()

获取到Class对象后，我们就可以获取方法信息，成员变量

获取成员变量：

* getFiled：访问公有的成员变量，包括父类的公有成员变量

* getDeclaredField：所有已声明的成员变量，但不能得到其父类的成员变量

获取方法集，主要有以下几个方法：

* getDeclaredMethods 方法返回类或接口声明的所有方法，包括公共、保护、默认（包）访问和私有方法，但不包括继承的方法。

* getMethods 方法返回某个类的所有公用（public）方法，包括其继承类的公用方法。

* getMethod 方法返回一个特定的方法，其中第一个参数为方法名称，后面的参数为方法的参数对应Class的对象。

创建实例

* 使用 Class 的 newInstance 方法，使用这个方法会抛出两个异常IllegalAccessException（非法访问异常） 和 InstantiationException（实例化异常）

* 先使用Class对象获取指定的Constructor对象, 再调用Constructor对象的newInstance()方法来创建该Class对象对应类的实例(通过这种方式可以选择指定的构造器来创建实例).

方法调用

通过 Method 的invoke（）方法实现方法调用。

```
Class cl = null;

try {

    cl = Class.forName("cn.assist.easydao.util.Student");

    Object o = cl.newInstance();

    Method setAge = cl.getDeclaredMethod("setAge", Integer.class);

    Method getAge = cl.getDeclaredMethod("getAge");

    setAge.invoke(o,3);

    System.out.println(getAge.invoke(o));



} catch (ClassNotFoundException e) {

    e.printStackTrace();

} catch (IllegalAccessException e) {

    e.printStackTrace();

} catch (InstantiationException e) {

    e.printStackTrace();

} catch (NoSuchMethodException e) {

    e.printStackTrace();

} catch (InvocationTargetException e) {

    e.printStackTrace();

}
```

实现通用的数组操作

// 创建数组

Object array = Array.newInstance(String.class,4);

// 设值

Array.set(array,0,"test");

# 4. 总结

Java反射会消耗大量资源？

* 由于反射创建对象是间接创建，需要做一些步骤操作，会导致执行时间长，消耗大量资源的。

如何提高反射效率？

* 使用缓存机制，避免重复创建对象

* 获取方法尽量使用getMethod(methodName)，不建议使用getMethods()后再遍历筛选

* 使用现成的反射框架

* 使用reflectasm框架，该框架通过字节码，访问类信息

反射可以在运行时操作，可能导致系统变的不安全

参考文档：

[深入解析Java反射（1） - 基础](https://www.sczyh30.com/posts/Java/java-reflection-1/)

Java 核心技术 卷一
