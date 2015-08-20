---
layout: post
title: "解决一个服务启动时报[Address already in use]的问题"
description: ""
category: "java"
tags: [java server]
---
{% include JB/setup %}

一个Java服务器重启失败，后台报了[Address already in use]的错误。  

第一个可能是，这个服务上一次没有stop成功，依然还活着，所以没有释放端口。ps之后发现确实是死了。  

第二个可能是，其他程序使用了跟这个服务一样的端口，netstat之后发现，端口根本没有被占用。  

这下有点头大，端口没有被使用，你报什么[Address already in use]啊。  
再仔细看log，报错误的地方是程序依赖的其他jar包中的代码报的，原来服务启动的时候会有一些代码被执行，这个错误是在连接其他服务的时候报错的，那应该就不是该服务本身所使用的端口了。不过连接其他服务也不可能使用固定的端口啊。  

不过既然是端口相关错误，那么还是netstat -anp出来所有的连接，看看有什么问题。

结果发现有两万多个连接某一台mysql测试服务器的记录，但基本是上TIME_WAIT状态，而不是ESTABLISHED状态。

这下问题清楚了，linux服务器可用的端口是有上限的，如果所有的端口都被用占用完了，而且又不能复用，那么就没有端口可供分配使用了。  

解决方法就是找出哪个程序在这么疯狂的使用mysql测试服务器，然后让那个服务解决疯狂占用的问题就好了。

**PS:**  
mysql被疯狂占用的问题是因为使用了spring的datasource[org.springframework.jdbc.datasource.DriverManagerDataSource]，这个datasource每次获取connection的时候都是新创建一个连接，而且那个程序还定时任务不停的调用数据库，所以创建了n多的mysql连接。
