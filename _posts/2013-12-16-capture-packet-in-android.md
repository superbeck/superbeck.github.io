---
layout: post
title: "Android使用抓包工具"
description: "Android如何使用抓包工具"
category: "android"
tags: [android, wireshark]
---
{% include JB/setup %}

* content
{:toc}

不管是研究别的app，还是怀疑手机中毒要查问题，抓包都是个好办法。
本文主要是参照[参考资料1]中的内容，做个记录。
<!--excerpt-->

## 1. Required
1.1. Android SDK是必须的，作为必备开发工具，默认应该都装了。[下载链接](http://developer.android.com/sdk/index.html)

1.2. 手机需要root。简单的方法是尝试各种一键root工具，我手头的一个SONY LT26w，用百度一键root没有成功，用360一键root成功。

1.3. 下载tcpdump (见参考资料)

## 2. Install
### 2.1. 连接手机和电脑，拷贝下载的tcpdump到手机上。

    $adb push 本机目录/tcpdump /data/local/tcpdump
如果没有权限，就把/data/local目录权限修改一下。

    $chmod 777 /data/local
换其他目录也行。
### 2.2. 给tcpdump可执行权限

    $adb shell
    $chmod 777 /data/local/tcpdump
## 3. 运行
3.1. 启动tcpdump

    $adb shell
    $su
不su的话，可能会得到错误信息\[no suitable device found\]

运行命令：

    $/data/local/tcpdump -p -vv -s 0 -w /sdcard/capture.pcap

3.2. 手机上运行要抓包的程序，执行完成后在命令提示符窗口执行Ctrl+C中断抓包进程

3.3. 把抓好的包拷贝到电脑上，用wireshark等抓包分析工具分析

    $adb pull /sdcard/capture.pcap
ubuntu上安装wireshark比较容易。直接执行命令：

    $sudo apt-get install wireshark 
## 4. 参考资料
1. [Android系统手机端抓包方法](http://www.cnblogs.com/rootq/archive/2012/04/08/2438262.html)
2. [tcpdump](http://www.strazzere.com/android/tcpdump)
3. [tcpdump百度云](http://pan.baidu.com/s/13pand)
4. [tcpdump115网盘](http://115.com/lb/5lbazj4gipj)
5. [adb pull和adb push失败问题解决方法](http://blog.sina.com.cn/s/blog_4b976b2d0100qnwa.html)
6. [tcpdump error: no suitable device found](http://www.unix.com/ip-networking/13981-tcpdump-error-no-suitable-device-found.html)
