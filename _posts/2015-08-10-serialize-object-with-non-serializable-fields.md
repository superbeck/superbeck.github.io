---
layout: post
title: "序列化含有不可序列化字段的java对象"
description: ""
category: "java"
tags: [java, seralization]
---
{% include JB/setup %}

*content
{:toc}

JDK的序列化机制要求被序列化的对象的类以及各字段的类都实现了Serializable接口。如果各个字段的类是自己定义的话，那么可以自己修改这些类型。如果不是呢？
<!--excerpt-->

## 1. 解决方案

JDK的方案是允许含有不可序列化字段的类实现如下方法

private void writeObject(ObjectOutputStream out)  
private void readObject(ObjectInputStream in)

另外一种方案是为每一个字段的类实现一个decorator类，然后所有的使用者都使用这个decorator类。


## 2. 方案的实现

这个方案的实现是这样的：

java.io.ObjectOutputStream#write(Object) ->  
writeObject0(Object, boolean) ->  
writeOrdinaryObject(Object, ObjectStreamClass, boolean) ->  
writeSerialData(Object obj, ObjectStreamClass desc) ->  
java.io.ObjectStreamClass#invokeWriteObject(Object obj, ObjectOutputStream out)


## 3. 参考资料
1. [怎样对带有不可序列化属性的Java对象进行序列化](http://www.importnew.com/10705.html) 原文链接[marxsoftware](http://marxsoftware.blogspot.com/2014/02/serializing-java-objects-with-non.html) 译者[ImprtNew](http://www.importnew.com/author/wangyiheng)
