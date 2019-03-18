---
title: Java 动态代理为啥需要接口？？？
date: 2019-03-15 09:43:16
tags:
- java
- 动态代理
---

带着问题，我们直接查看源码：
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
现在我们终于得到答案，为啥动态代理需要使用接口，主要原因是代理类已经继承了Proxy类，Java不支持多继承所以导致动态代理需要使用接口，接口支持多继承。终于可以说服自己为啥动态代理需要接口了。。。。。

参考文档：
jdk 8
