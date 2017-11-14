---
layout: post
title: "Java服务log中有异常信息却没有stack trace的原因和解决方法"
title-en: "Exception In Log With No StackTrace"
description: ""
category: "java"
tags: [java, jvm, exception, server, stack trace]
---

* content
{:toc}

## 1. 问题描述

在线上的服务中，有时候会发现log中会输出异常信息，但是只有一行异常类的全名，却没有任何stack trace信息, 比如下面这一行log。
而我们在使用log4j打印信息的时候却是要求打印堆栈出来的。
那这是怎么回事呢？

```
java.lang.NullPointerException
```
<!--excerpt-->

## 2. 原因解析
其实这是因为JDK1.5的JVM新增加的一个优化。

JDK 1.5的Release Notes中的原话是这样的:
```
The compiler in the server VM now provides correct stack backtraces for all "cold" built-in exceptions. For performance purposes, when such an exception is thrown a few times, the method may be recompiled. After recompilation, the compiler may choose a faster tactic using preallocated exceptions that do not provide a stack trace. To disable completely the use of preallocated exceptions, use this new flag: -XX:-OmitStackTraceInFastThrow.
```

意思是说，对于那些“冷”的内置异常来说，JVM会记录异常抛出的次数，达到一定次数之后，就会重编译，选择一个不提供stack trace的预分配异常对象。
这样，我们在catch到异常并且打印异常log的时候，就只有一行(这一行应该是toString()方法返回的String)，而不会有stace trace输出了。

PS: built-in exceptions是指JDK中的异常类，但是"冷"这个词不太懂。

## 3. 解决方法
按照[Release Notes]中的说法，我们应该在JVM参数中增加个flag[-XX:-OmitStackTraceInFastThrow]就可以了。
另外一种方法是JVM中增加参数[-Xint], 选择按字节码解释执行，而不是编译成native code执行。

这两种方法都是可以解决问题的，但是不推荐。因为性能影响不得不考虑。
最合理的办法是回溯以往的log, 找到异常最开始打印的地方，那个时候是有完整的stace trace输出的。
另外也可以考虑重启服务后，找到最新的异常log的stace trace分析。

## 4. 参考资料
1. [JDK 1.5 Release Notes](http://www.oracle.com/technetwork/java/javase/relnotes-139183.html)
2. [Hotspot caused exceptions to lose their stack traces in production – and the fix](http://jawspeak.com/2010/05/26/hotspot-caused-exceptions-to-lose-their-stack-traces-in-production-and-the-fix/)
