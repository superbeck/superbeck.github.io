---
layout: post
title: "《Android开发视频教程（重制版）》第二季学习"
description: ""
category: ""
tags: []
---
{% include JB/setup %}

### 第零集
subtitle: 课程介绍
URL:http://www.marschen.com/forum.php?mod=viewthread&tid=15718&extra=page%3D1
主要内容：
Http, 权限, Intent, HttpClient, Thread, looper, Handler, Activity, Message, HttpRequest

### 第一集
subtitle: Activity生命周期（一）
URL: http://www.marschen.com/forum.php?mod=viewthread&tid=15895&extra=page%3D1
主要内容:
如何在一个应用程序中定义多个activity
启动一个activity的方法
android当中的back stack


### 第二集
subtitle: Activity的生命周期（二）
URL: http://www.marschen.com/forum.php?mod=viewthread&tid=16199&extra=page%3D1
主要内容：
什么是生命周期
Activity的生命周期函数
生命周期函数的调用时机
onCreate onStart onResume onPuase onStop onDestroy onRestart
--创建A，A.onCreate() -> A.onStart() -> A.onResume()
--弹出B, A.onPause() -> B.onCreate() -> B.onStart() -> B.onResume() -> A.onStop() (A入栈)
--后退， B.onPause() -> A.onRestart() -> A.onStart() -> A.onResume() -> B.onStop() -> B.onDestroy() (B销毁，Ａ出栈)

### 第三集
subtitle: Activity生命周期（三）
URL: http://www.marschen.com/forum.php?mod=viewthread&tid=16340&extra=page%3D1
主要内容:
Activity对象的状态 (Resumed, Paused, Stopped)
成对儿的生命周期函数
一个相当无厘头的例子


### 第四集
subtitle: 
URL: 
主要内容：

### 第五集
subtitle: Android当中的线程 
URL: http://www.marschen.com/forum.php?mod=viewthread&tid=16703&extra=page%3D2
主要内容：
回顾java当中的线程概念
--线程的两种实现方式
--线程的生命周期
--多线程同步
Main Thread和Worker Thread
Worker Thread不能调用view的函数对view进行设置，但是ProgressBar.setProgress()是可以的。
Main Thread不能调用需要长时间运行的函数，容易引ANR问题。
Android当中的线程使用

### 第六集(未观看)
subtitle: Handler(一)
URL:http://www.marschen.com/forum.php?mod=viewthread&tid=16812&extra=page%3D1
主要内容：
什么是Handler
Handler，Looper和MessageQueue的基本原理
一个简单的Handler例子


### 第七集(未观看)
subtitle: Handler（二上）
URL: http://www.marschen.com/forum.php?mod=viewthread&tid=16863&extra=page%3D1
主要内容：
通过Handler实现线程间通信
在主线程当中实现Handler的handleMessage()方法
在Worker Thread当中通过Handler发送消息


### 第八集(未观看)
subtitle: Handler（二下）
URL:http://www.marschen.com/forum.php?mod=viewthread&tid=17085&extra=page%3D1
主要内容：
准备Looper对象
在Worker Thread中生成handler对象
在MainThread中发送消息

### 第九集
subtitle:
URL:
主要内容：


### 第十集
subtitle:
URL:
主要内容：


### 第十一集
subtitle:图片视图（ImageView）
URL:http://www.marschen.com/forum.php?mod=viewthread&tid=14990&extra=page%3D1
主要内容：
图片视图(ImageView)的基本概念
<ImageView/>与ImageView 
神奇的ScaleType属性


### 第十二集
subtitle:深入LinearLayout
URL:http://www.marschen.com/forum.php?mod=viewthread&tid=15206&extra=page%3D1
主要内容：
LinearLayout布局的嵌套
奇葩的layout_weight属性
--同一空间内的几个控件的layout_weight属性都是1,则是把剩余空间平均分配给这几个控件，而不是说这几个控件平均占有整个空间。
--两个控件，A的layout_weight为1,B为2的话，则是把剩余空间平均分成3份，A加1/3，B加2/3。
--A,B的layout_width为"0dp"的话，那么A占整个空间的1/3，B占2/3。

### 第十三集
subtitle:
URL:
主要内容：

### 第十四集
subtitle:
URL:
主要内容：



------------------------------template-------------------------------
### 第集
subtitle: 
URL:
主要内容：


