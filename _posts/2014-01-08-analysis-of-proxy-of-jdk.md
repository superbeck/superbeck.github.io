---
layout: post
title: "分析JDK中的动态代理"
description: ""
category: "design"
tags: [java 动态代理 proxy InvocationHandler]
---
{% include JB/setup %}

代理模式是一种比较常用的设计模式，AOP编程的核心。代理对象本身不做业务逻辑相关的事情，一般是环绕原业务逻辑做一些周边的事情，比如说打log，计算时间消耗，进行权限判断，各种事前事后的处理。这样的方式可以实现对一批不同的类的对象做相同的事情。

根据代理对象的创建时间区分，代理模式分静态代理和动态代理。

静态代理一般是java代码早已存在(程序员创建或者自动生成)，运行前已经存在class文件。

动态代理一般是在程序运行期间，由其他代码运用反射机制生成。


#### 1. 代码示例
其中JDK给我们提供了一套动态代理的机制，有一个类java.reflect.Proxy和一个接口java.reflect.InvocationHandler。

其中Proxy提供了生成代理对象的接口，并且它是所有生成的代理对象的父类; InvocationHandler是代理逻辑的核心实现类的父接口。

下面的代码是一个简单的示例。

**将要被代理的接口**

```java
package wsf.test.dao;

/**
 * A sample interface for proxy test.
 *
 * @author superbeck
 * @since Jan 9, 2014
 */
public interface AbcDAO {
    String getName(int id);

    String[] getNames();
}
```

**实现被代理接口的子类，用于测试，非必要**

```java
package wsf.test;

import wsf.test.dao.AbcDAO;

/**
 * A sample of concreate subclass of {@link AbcDAO} for proxy test.
 * 
 * @author superbeck
 * @since Jan 9, 2014
 */
public class AbcDAOImpl implements AbcDAO {

    @Override
    public String getName(int id) {
        return "myname";
    }

    @Override
    public String[] getNames() {
        return new String[] { "myname1", "myname2" };
    }
}
```

**代理逻辑核心实现类，在方法调用前后记录调用时间**

```java
package wsf.test;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

import wsf.test.dao.AbcDAO;

/**
 * A sample of subclass of {@link InvocationHandler} for proxy test.
 * 
 * @author superbeck
 * @since Jan 9, 2014
 */
public class MyLogInvocationHandler implements InvocationHandler {

    private AbcDAO dao;

    public MyLogInvocationHandler(AbcDAO dao) {
        this.dao = dao;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("call before method #" + method.getName() + " on "
                + System.currentTimeMillis());

        Object result = method.invoke(dao, args);

        System.out.println("call after method #" + method.getName() + " on "
                + System.currentTimeMillis());
        return result;
    }
}
```

**测试代理功能的客户端代码**

```java
package wsf.test;

import java.lang.reflect.Proxy;
import java.util.Arrays;

import wsf.test.dao.AbcDAO;

/**
 * A sample client code for proxy test.
 * 
 * @author superbeck
 * @since Jan 9, 2014
 */
public class TestProxy {

    public static void main(String[] args) {
        AbcDAOImpl daoImpl = new AbcDAOImpl();
        MyLogInvocationHandler handler = new MyLogInvocationHandler(daoImpl);
        AbcDAO dao = (AbcDAO) Proxy.newProxyInstance(daoImpl.getClass().getClassLoader(),
                new Class[] { AbcDAO.class }, handler);
        String[] names = dao.getNames();
        System.out.println(Arrays.asList(names));
    }
}
```

#### 2. 机制分析
如果对上面这段代码有疑问的话，估计最核心的问题是：对代理对象的调用最终怎么会调用到自己的InvocationHandler的子类的invoke方法？

一句话回答这个问题：Proxy动态生成了一个类放在内存中，这个类继承Proxy且实现了所有指定的Interface的类，而且唯一的构造函数的参数是InvocationHandler，对这个代理类的任何方法的调用都被转到调用这个InvocationHandler对象的invoke方法，然后根据这个新的类创建一个对象返回给外部使用。

如果要详细了解，可以看下面对JDK源代码的分析。

其中java.lang.reflect.Proxy.newProxyInstance()的核心代码如下：

```java
/*
 * Look up or generate the designated proxy class.
 */
Class cl = getProxyClass(loader, interfaces);

/*
 * Invoke its constructor with the designated invocation handler.
 */
try {
    Constructor cons = cl.getConstructor(constructorParams);
    return (Object) cons.newInstance(new Object[] { h });
```

另外

```java
/** parameter types of a proxy class constructor */
private final static Class[] constructorParams = { InvocationHandler.class };
```

而Proxy.getProxyClass的核心逻辑是

```java
String proxyName = proxyPkg + proxyClassNamePrefix + num;

byte[] proxyClassFile =	ProxyGenerator.generateProxyClass(
		    proxyName, interfaces);

try {
    proxyClass = defineClass0(loader, proxyName,
	proxyClassFile, 0, proxyClassFile.length);
```

其中sun.misc.ProxyGenerator#generateProxyClass()中的逻辑就是动态构造了一个类，其中有一个参数为InvocationHandler的构造函数，至于被代理接口的每个方法都对应的增加了一个实现。具体请参照[ProxyGenerator][1]。

其中getNames()的实现代码如下，h是这个类的父类java.reflect.Proxy的protected属性，也就是java.lang.reflect.Proxy.newProxyInstance()中传入的InvocationHandler对象。

```java
return (String[])this.h.invoke(this, m4, null);
```
从以上分析，就可以知道对一个代理对象的调用怎么会转到对InvocationHandler对象的调用了。

#### 3. 其他
想知道动态生成的class文件是什么内容？

其实，经过一番小设置，是可以把动态生成的class文件生成出来的。

在启动java程序的时候加一个参数，这样就会在程序启动的目录下生成一批以"$Proxy"+数字的方式命名的class文件，用java反编译软件就能查看大致的代码了。

```
-Dsun.misc.ProxyGenerator.saveGeneratedFiles=true
```

当然了，也可以在你的main函数的最前面加这样一行代码，效果是一样的。

```java
System.setProperty("sun.misc.ProxyGenerator.saveGeneratedFiles", "true");
```

以下是针对interface wsf.test.dao.AbcDAO的代理类的完整的反编译后的代码，可以参照一下。

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.lang.reflect.UndeclaredThrowableException;
import wsf.test.dao.AbcDAO;

public final class $Proxy1 extends Proxy
  implements AbcDAO
{
  private static Method m4;
  private static Method m1;
  private static Method m0;
  private static Method m3;
  private static Method m2;

  public $Proxy1(InvocationHandler paramInvocationHandler)
    throws 
  {
    super(paramInvocationHandler);
  }

  public final String[] getNames()
    throws 
  {
    try
    {
      return (String[])this.h.invoke(this, m4, null);
    }
    catch (Error|RuntimeException localError)
    {
      throw localError;
    }
    catch (Throwable localThrowable)
    {
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  public final boolean equals(Object paramObject)
    throws 
  {
    try
    {
      return ((Boolean)this.h.invoke(this, m1, new Object[] { paramObject })).booleanValue();
    }
    catch (Error|RuntimeException localError)
    {
      throw localError;
    }
    catch (Throwable localThrowable)
    {
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  public final int hashCode()
    throws 
  {
    try
    {
      return ((Integer)this.h.invoke(this, m0, null)).intValue();
    }
    catch (Error|RuntimeException localError)
    {
      throw localError;
    }
    catch (Throwable localThrowable)
    {
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  public final String getName(int paramInt)
    throws 
  {
    try
    {
      return (String)this.h.invoke(this, m3, new Object[] { Integer.valueOf(paramInt) });
    }
    catch (Error|RuntimeException localError)
    {
      throw localError;
    }
    catch (Throwable localThrowable)
    {
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  public final String toString()
    throws 
  {
    try
    {
      return (String)this.h.invoke(this, m2, null);
    }
    catch (Error|RuntimeException localError)
    {
      throw localError;
    }
    catch (Throwable localThrowable)
    {
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  static
  {
    try
    {
      m4 = Class.forName("wsf.test.dao.AbcDAO").getMethod("getNames", new Class[0]);
      m1 = Class.forName("java.lang.Object").getMethod("equals", new Class[] { Class.forName("java.lang.Object") });
      m0 = Class.forName("java.lang.Object").getMethod("hashCode", new Class[0]);
      m3 = Class.forName("wsf.test.dao.AbcDAO").getMethod("getName", new Class[] { Integer.TYPE });
      m2 = Class.forName("java.lang.Object").getMethod("toString", new Class[0]);
      return;
    }
    catch (NoSuchMethodException localNoSuchMethodException)
    {
      throw new NoSuchMethodError(localNoSuchMethodException.getMessage());
    }
    catch (ClassNotFoundException localClassNotFoundException)
    {
      throw new NoClassDefFoundError(localClassNotFoundException.getMessage());
    }
  }
}
```

### 参考资料
1. [sun.misc.ProxyGenerator][1]


[1]: http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/6-b14/sun/misc/ProxyGenerator.java#ProxyGenerator.generateClassFile%28%29 "sun.misc.ProxyGenerator"

