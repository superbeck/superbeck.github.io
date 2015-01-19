---
layout: post
title: "IP地址的转换和判断"
description: ""
category: ""
tags: [IP]
---
{% include JB/setup %}

####IP地址的的表示法
IP地址的已知的表示方法有两种,一是我们常见的点分表示法(100.100.100.100),另外一种是十进制表示法(1684300900是100.100.100.100的十进制表示法).

####两种表示法的转换规则
假设点分表示法的IP地址是a.b.c.d,十进制的表示法的值为:16777216 * a + 65536 * b + 256 * c + d.

也等于2^24 * a + 2^16 * b + 2^8 * c + d.


**反过来的转换规则**

a = int ( IP Number / 16777216 ) % 256

b = int ( IP Number / 65536    ) % 256

c = int ( IP Number / 256      ) % 256

d = int ( IP Number            ) % 256


####reference
1. [IP地址点分表示法与十进制表示法的转换](http://www.cnblogs.com/zhumk/archive/2005/05/11/57656.html)
