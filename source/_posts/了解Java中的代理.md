---
title: 了解Java中的代理
date: 2019-03-15 09:46:49
tags:
- JAVA
---
# 1. 什么是代理？
代理能代替我们去做很多事情，就举一个买票的例子，我们如果去火车站买票，需要搭车去火车站，然后排队买票，买到票回家这一系列的操作都需要自己去完成，万一碰到每票的情况还需要明天继续跑一趟。这时你是不是想有一个人帮你去做，做成之后你给他一点报酬，那么你是不是不用去火车站买票了，可以做一些自己的事了。代替你去买票的过程其实就是代理的过程。
# 2. 代理的实现？
在没有使用代理之前，我们需要自己去实现很多功能，现在我通过工人工作和打卡两个功能进行说明
* 工人接口 有一个默认方法打卡和一个工作的方法
```
public interface Worker {
	public void work();
    default void punch() {
        System.out.println("上下班打卡。。。。。。。。。。。。。。。");
    }
}
```
* 工人A 实现类 ，实现接口的工作方法，并在上下班前打卡
```
/**
 * 第一个工人
 * @author xla
 */
public class WorkFirst implements Worker {

    @Override
    public void work() {
        punch();
        System.out.println("doing First work");
        punch();
    }

}
```
* 工人B 实现类，实现接口的工作方法，并在上下班前打卡
```
/**
 * 第二个工人
 * @author xla
 */
public class WorkSecond implements Worker {

    /**
     * 他们在工作前后需要打卡
     */
    @Override
    public void work() {
        punch();
        System.out.println("do second job");
        punch();
    }
}
```
上述代码在没有使用代理前是这么实现的，但是你有没有发现一个问题，上述工人在工作前后都调用了punch方法，显得有些重复。那我们通过什么可以解决这个问题呢？代理？？

目前我们使用的Java 代理有两种，静态代理和动态代理下面我们通过例子来实现：
* 静态代理
我们创建一个代理类 WorkProxy
```
public class WorkProxy implements Worker {
    private Worker worker;

    public WorkProxy(Worker worker) {
        this.worker = worker;
    }

    @Override
    public void work() {
          punch();
          worker.work();
          punch();
    }
}
```
工人A 中的work 方法就不需要再前后调用打卡功能，只需要将自己的对象交给代理类让代理类去实现打卡功能代码如下：
```
public static void main(String[] args) {
        Worker worker = new WorkFirst();
        WorkProxy workProxy = new WorkProxy(worker);
        workProxy.work();
 }
```
结果如下：
```
上下班打卡。。。。。。。。。。。。。。。
doing First work
上下班打卡。。。。。。。。。。。。。。。
```
* 动态代理

动态代理，顾名思义就是代理类是在运行过程中动态创建的，实现动态的过程我们需要创建一个中介类，实现InvocationHandler 接口作为调用处理器”拦截“对代理类方法的调用。中介类实现如下：
```
public class WorkDynamicProxy implements InvocationHandler {
    private Worker worker;

    public  WorkDynamicProxy(Worker worker) {
        this.worker = worker;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        worker.punch();
        Object result = method.invoke(worker, args);
        worker.punch();
        return result;
    }
}
```
接下来我们来测试一下：
```
public static void main(String[] args) {
        WorkDynamicProxy workDynamicProxy = new WorkDynamicProxy(new WorkSecond());
        //获取代理类实例worker
        Worker worker = (Worker)(Proxy.newProxyInstance(Worker.class.getClassLoader(), new Class[] {Worker.class}, workDynamicProxy));
        //通过代理类对象调用代理类方法，实际上会转到invoke方法调用
        worker.work();
    }
```
打印结果如下：
```
上下班打卡。。。。。。。。。。。。。。。
do second job
上下班打卡。。。。。。。。。。。。。。。
```
现在我们需要考虑一几个问题？动态代理的过程？为啥动态代理需要使用接口？现在我们来解决第一个问题：
* 动态代理的过程？
从上述代码中我们可以看出，通过newProxyInstance方法获取一个代理类的，调用代理类的方法都会执行invoke(Object proxy, Method method, Object[] args)方法，解释三个参数
proxy 委托类
method 代理类的方法
args 调用代理类的方法参入的参数
* 为啥动态代理需要使用接口？
我们再次查看源码newProxyInstance方法的实现：
```
public static Object newProxyInstance(ClassLoader loader,
                                          Class<?>[] interfaces,
                                          InvocationHandler h)
        throws IllegalArgumentException
    {
        Objects.requireNonNull(h);

        final Class<?>[] intfs = interfaces.clone();
        final SecurityManager sm = System.getSecurityManager();
        if (sm != null) {
            checkProxyAccess(Reflection.getCallerClass(), loader, intfs);
        }

        /*
         * Look up or generate the designated proxy class.
         */
        Class<?> cl = getProxyClass0(loader, intfs);

        /*
         * Invoke its constructor with the designated invocation handler.
         */
        try {
            if (sm != null) {
                checkNewProxyPermission(Reflection.getCallerClass(), cl);
            }

            final Constructor<?> cons = cl.getConstructor(constructorParams);
            final InvocationHandler ih = h;
            if (!Modifier.isPublic(cl.getModifiers())) {
                AccessController.doPrivileged(new PrivilegedAction<Void>() {
                    public Void run() {
                        cons.setAccessible(true);
                        return null;
                    }
                });
            }
            return cons.newInstance(new Object[]{h});
        }
        ....
    }
```
从源码上，首先进行一系列的判断逻辑，然后到了
Class<?> cl = getProxyClass0(loader, intfs);
获取代理类的Class 对象，接下来执行
 final Constructor<?> cons = cl.getConstructor(constructorParams);
 获取Class的构造对象，通过cons.newInstance(new Object[]{h}); 返回一个代理类实例对象，具体参考 [Java反射](http://www.xulian.net.cn/2019/02/20/java-%E5%8F%8D%E5%B0%84%E7%AC%94%E8%AE%B0/) 。
 目前我们只知道代理类对象的创建过程，但是为啥只能传入接口还是得不到解决，那我们只能继续查看getProxyClass0源码：
 具体实现步骤：
 1. 验证，通过类加载对象和接口列表获取代理类的Class对象，如果缓存中存在则返回，没有就通过代理类工厂创建代理类
 2. 创建代理类
通过ProxyClassFactory工厂创建代理类，核心代码如下
```
String proxyName = proxyPkg + proxyClassNamePrefix + num;
byte[] proxyClassFile = ProxyGenerator.generateProxyClass(proxyName, interfaces, accessFlags);
defineClass0(loader, proxyName, proxyClassFile, 0, proxyClassFile.length);
```
定义一个类的名称，通过传入的接口列表和访问标志生成一个代理类的字节码，最后调用defineClass0方法将字节码加载到类加载器中，创建一个代理类。
现在我们来实现将代理类的字节码写入本地，查看具体代理类的信息：
```
byte[] proxyClassFile = ProxyGenerator.generateProxyClass(
                "$proxy1", new Class[] {Worker.class}, Modifier.PUBLIC);
        FileOutputStream fos = null;
        try {
            fos = new FileOutputStream("D:/$proxy1.class");
            fos.write(proxyClassFile);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                fos.flush();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
```
通过反编译启查看字节码：
```
public class $proxy1 extends Proxy  implements Worker {
....
public final void work()  throws  {
    try  {
      this.h.invoke(this, m3, null);
      return;
    }
    ....
  }
....
}
```
现在我们终于得到答案，this.h.invoke(this, m3, null); 很好得验证了之前执行work（）方法其实是调用了中介类的invoke方法结论，还有一个问题，为啥动态代理需要使用接口，主要原因是代理类已经继承了Proxy类，Java不支持多继承所以导致动态代理需要使用接口，接口支持多继承。终于可以说服自己为啥动态代理需要接口了。。。。。
# 3. 总结
我们大概讲了一下什么是代理，然后通过例子，代码实现了Java中的代理，最后通过源码解释了Java动态代理的过程。。。


参考文档：

jdk 8
[JDK动态代理为什么必须针对接口？其与CGLIB的对比](https://www.cnblogs.com/frankliiu-java/articles/1896443.html)
