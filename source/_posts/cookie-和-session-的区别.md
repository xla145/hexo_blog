---
title: cookie 和 session 的区别
date: 2019-02-26 09:42:48
tags:
- http
- 网络安全
---
今天我们来讲一下 cookie和 session的一些区别，下面通过几个方面来说明它们之间的差异？
# 1. 为啥需要session和cookie?
由于http是无状态的，如果需要追踪用户的记录主要引入session 追踪用户，一般我们常用session 记录用户的登录态，存放用户的一些信息 主要用于用户浏览其他页面时对用户登录态的判断。
# 2. 存在位置
cookie 是基于client端 而 session 基于 server端
# 3.系统安全性
   由于基于client，存在浏览器上所以可能会导致cookie被窃取导致密码别泄露，出现这种原因主要是在公用网络下操作或者一些不安全的网络。其实这种情况下cookie被窃取也会导致session不安全，因为一般情况下，session key（session ID 用于标识用户，一般情况下 session 有效时间是30分钟和关闭浏览器失去登录态，session 失效主要原因在于session id 失效，重新打开浏览器会重新获取新的session ID） 存储在cookie中，会导致session不安全，还有一种情况就是存放在URL中，这种方式session ID 直接暴露在URL 更不安全，其实安全这问题都是相对的，下面我们讲一下如何使系统变的安全

  1. 保护好自己的cookie，在非安全的网络下，禁止将账号密码存在cookie中
  2. 通过程序来保障安全，session 记录用户登录的浏览器的 UA 头和ip地址等信息，在一些敏感操作下验证登录时浏览器的 UA 头和ip地址等信息，判断是否一致来确定安全。
  3. 程序中尽量使用一些框架来管理好session，对session做一个监控，定期清除session信息。
  4. 确定的关闭方式，当用户退出登录时，清除cookie 中的session ID ，session 存储的内容等信息。
  5. 尽量将网站从http变成https，这样传输中可以避免cookie被劫持。
  6. cookie的过期时间不能设置过长，cookie和session 过期时间尽量设置一样，一旦cookie过期了 session ID 也跟着过期，但是有一种情况，session当用户刷新页面，session 会一直续期。虽然前面说的cookie过期了，其实 session ID 还没有过期这样也会导致问题出现。
  小结：安全问题是相对的，主要你做到上面几点，安全问题自然对你影响不大。

# 4. 存储内容大小

1. 浏览器对cookie数量和cookie存储大小有限制，下面对不同浏览器cookie限制做一个介绍
| |IE6.0  |IE7.0/8.0  | Opera | Chrome | Safari
|cookie个数|每个域为20个|每个域为50个|每个域为30个|每个域为53个|没有个数限制|
|cookie大小|4095个字节|	4095个字节|4096个字节|4097个字节|4097个字节|
总之对页面cookie 操作，尽量数量小于20总大小小于4kb，过期时间不能设置过长，避免内容过多导致浏览器自动清除内容把一些有用的字段如 session ID 导致登录态丢失，需要重新登录

2. session 存在服务器中，对小于没有很严格的限制，但是也不能存放过多的内容，主要是服务器内存有限一旦创建过多的内容占据大量的内存，会导致内存不足等情况出现，影响其他操作。

# 5. 总结
session和cookie在项目中需要结合使用，了解它们的区别进而能更好的利用它们

参考文档：
1. [Cookie个数限制及大小](https://my.oschina.net/gaollg/blog/71299)
2. [正确理解 Session 的安全性](https://www.jianshu.com/p/c4b32eb24894)
