---
layout: post
title: "如何在Eclipse中关联Android源代码"
description: ""
category: "android"
tags: [android, eclipse]
---
{% include JB/setup %}

* content
{:toc}

在eclipse中新建一个android工程时，会自动依赖android的jar文件，但是没有关联source code。

怎么来实现这个关联呢？按照以下步骤是可以的。
<!--excerpt-->
### 1. 下载对应版本的源代码
启动Android SDK Manager，选择具体版本的\[Sources for Android SDK\]，下载到本地。

### 2. 在eclispe的project中关联source code
右键点击工程 -> 属性 -> Java Build Path -> Libraries, 里面的小窗口中会显示依赖了android的jar。

展开Android-x.x.x -> android.jar，选中\[Source attachment\]，点击右侧的\[Edit...\]按钮，在弹出的小窗口上点击\[External Folders...\]，选择\[android-sdk/sources/android-XX\]，再一路确定即可。

### 注意
Android SDK Manager中只能下载到4.0.3版(API 15)的源代码。

想要下载更早的版本只能自己去网上搜索相应的源代码包了。
