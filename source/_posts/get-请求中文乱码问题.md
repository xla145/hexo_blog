---
title: get 请求中文乱码问题
date: 2019-02-22 09:49:52
tags:
- springMvc
- 字符编码
---
对于浏览器发出的HTTP请求： tomcat 默认是ISO-8859-1 解码

   URI： xxxxx.action?key="+encodeURIComponent(“中文”)

   在服务器获取 到key的值是tomcat使用 ISO-8859-1编码进行解码的字符，会出现乱码的问题。目前有两种方式可以解决该问题。

    1：通过转成编码为ISO-8859-1字节数组，然后再转成UTF-8的字符串

 String value= new String(request.getParameter("key").getBytes("ISO-8859-1"), "UTF-8"); 

    2：在页面上进行两次编码

        xxxxx.action?key="+encodeURIComponent（encodeURIComponent(“中文”)）

后台获取数据： String value= URLDecoder.decode( request.getParameter("key"),"UTF-8")

实现原理：

.encodeURL函数主要是来对URI来做转码，它默认是采用的UTF-8的编码.

. UTF-8编码的格式:一个汉字来三个字节构成，每一个字节会转换成16进制的编码，同时添加上%号.

假设页面端输入的中文是一个“中”，按照下面步骤进行解码

    1.第一次encodeURI，按照utf-8方式获取字节数组变成[-28,-72-83]，对字节码数组进行遍历，把每个字节转化成对应的16进制数，这样就变成了[E4,B8,AD],最后变成[%E4,%B8,%AD]  此时已经没有了多字节字符，全部是单字节字符。

    2、第二次encodeURI，进行编码，会把%看成一个转义字符，并不编码%以后字符，会把%编码成%25.把数组最后变成[%25E4,%25B8,%25AD]然后就把处理后的数据[%25E4,%25B8,%25AD]发往服务器端，

    当应用服务器调用getParameter方法，getParameter方法会去向应用服务器请求参数应用服务器最初获得的就是发送来的[%25E4,%25B8,%25AD]，应用服务器会对这个数据进行URLdecode操作，应用服务器进行解码的这一次，不管是按照UTF-8，还是GBK，还是ISO-8859，,都能得到[%E4,%B8,%AD]，因为都会把%25解析成%.并把这个值返回给getParameter方法

    3、再用UTF-8解码一次，就得到"中"了。

