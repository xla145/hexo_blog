---
title: spring bean的作用域
date: 2019-03-15 09:51:36
tags:
- spring
---

Spring 中有五种作用域，但是有三种只适用在web环境![在这里插入图片描述](https://img-blog.csdnimg.cn/20190305163713526.png)
下面我们来介绍一下 singleton 和 prototype
* Singleton 作用域
我们先通过idea 创建一个项目，然后创建一个NewCar 类拥有两个方法如下：
```
public class NewCar {
    private String content;
    public void prints() {
        System.out.println(content);
    }
   	public void setContent(String content) {
        this.content = content;
    }
}
```
接着创建一个singleton.xml 配置文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="testSingleton" class="cn.assist.easydao.util.NewCar" scope="singleton"></bean>
</beans>
```
接下来我们创建一个测试类，来检验
```
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("singleton.xml");
NewCar newCar = (NewCar) applicationContext.getBean("testSingleton");
newCar.setContent("test singleton");
NewCar newCar1 = (NewCar) applicationContext.getBean("testSingleton");
newCar1.prints();
```
通过上述方法，打印结果如下：
```
test singleton
```
说明 newCar 和 newCar1 指向的是同一个实例，如果使用prototype作用域会是一个什么结果？
将上述配置文件scope 修改成 prototype，如下：
* prototype 作用域
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="testSingleton" class="cn.assist.easydao.util.NewCar" scope="prototype"></bean>
</beans>
```
再次运行测试类，打印如下
```
Null
```
通过结果我们很容易知道，newCar1 和 newCar 指向的不是同一个实例。
通过上述例子我们知道了singleton和prototype的区别，以后在实际过程中就能很好的利用它们了。
* Request 作用域
对于每个http请求，spring容器都会创建一个新的实例，由于每个请求都会创建一个新实例，相互之间不能看到对方的内部变化，所以相对来说是安全的。一旦请求完成，对应Request 作用域的实例也随着丢弃。
* Session 作用域
	每个请求都会创建一个实例，每个实例共享一个HTTP  Session。
. globalSession 作用域
普遍适用于在基于portlets 的web环境下，所有的portlet共享全局的session。
**注意：如果在基于 servlet 的web环境下，定义一个或者多个 global session 作用域，标准的http讲使用Session 作用，不会出现错误。**
参考文档：
[Bean scopes](https://docs.spring.io/spring/docs/3.0.0.M3/reference/html/ch04s04.html)

接下来一篇我们来介绍一下一个Servlet 和 portlets 的区别