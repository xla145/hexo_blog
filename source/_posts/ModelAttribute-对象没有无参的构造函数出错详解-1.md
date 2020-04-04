---
title: '@ModelAttribute 对象没有无参的构造函数出错详解'
date: 2019-02-18 17:15:47
tags:
---
如果对象没有无参的构造函数 @ModelAttribute 获取参数会报异常

![ModelAttribute-对象没有无参的构造函数出错详解](https://img.mupaie.com/20180627151839209.png)

 

通过反射出来的对象的参数类型（无参构造函数）和 类中构造函数（只有有参构造函数，没有无参构造函数 注释：当类中没有有参构造函数，会默认有一个无参构造函数，没有则无无参构造函数）的参数类型对比，

![ModelAttribute-对象没有无参的构造函数出错详解](https://img.mupaie.com/20180628105206677.png)

参数对比 ，两者不一致 返回false

![ModelAttribute-对象没有无参的构造函数出错详解](https://img.mupaie.com/20180628105954917.png)

最后抛出没有方法的异常信息

