---
layout: post
title: "如何反编译android应用程序的apk文件"
description: "介绍采用什么工具和方法反编译一个android的apk文件"
category: "android"
tags: [android]
---
{% include JB/setup %}

学习他人的android app，反编译是必不可少的步骤。根据目前可以找到的工具所限，反编译apk文件一共分两步。
提取资源文件和从dex文件提取出class文件，用java反编译软件查看class文件或者反编译为java文件。

### 1. 提取资源文件
到apktool的网站上下载\[apktool1.5.2.tar.bz2\]和\[apktool-install-linux-r05-ibot.tar.bz2\]。其中版本和操作系统请注意根据自己情况选择。(见参考资料)
从\[apktool1.5.2.tar.bz2\]解压出apktool.jar，从\[apktool-install-linux-r05-ibot.tar.bz2\]解压出aapt和apktool，把这三个文件放到一个文件夹中。
运行apktool可以得到帮助信息。
运行以下命令可以解压缩后的目录，可以查看各种资源文件。

    $ ./apktool d \[apk文件\] \[解压缩文件夹\]

### 2. 反编译dex文件
把apk文件重命名为zip，解压缩得到其中的classes.dex文件。
到dex2jar的网站上下载\[dex2jar-0.0.9.15.zip\]，解压缩。
执行以下命令可以反编译dex文件为class文件，然后就可以使用java反编译软件查看源代码了。

    $ ./d2j-dex2jar.sh classes.dex
当然，很多android apk都被混淆了，想看懂不容易。

### 3. java反编译软件
反编译软件没有什么好说的，参考资料4和5就是，都有gui版本和eclipse插件。
其中JD支持jdk1.5以上的class文件的解析，JAD是比较旧了，JD反编译失败的可以用JAD试试看不一定成功。

### 4. 其他
apktool除了可以反编译apk文件之外，也可以打包apk文件。

    $ ./apktool b \[原解压缩文件夹\]
一些盗版软件估计就是用这样的工具对想要复制的app先解压缩然后修改logo、一些文案后，再重编译发布一个貌似属于自己的app。
各位用来学习就行了，盗版就算了。偶尔用来去去广告还是可以接受的。

### 5. 参考资料
1. [用apktool和dex2jar反编译](http://blog.csdn.net/yueyueniao96/article/details/7540224)
2. [android-apktool](http://code.google.com/p/android-apktool/downloads/list)
3. [dex2jar](http://code.google.com/p/dex2jar/)
4. [JD Java Decompiler-JD-GUI](http://jd.benow.ca/)
5. [JAD Java Decompiler](http://varaneckas.com/jad/)
