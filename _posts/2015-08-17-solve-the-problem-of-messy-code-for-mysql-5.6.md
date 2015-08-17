---
layout: post
title: "mysql 5.6遇到乱码问题的一点解决经验"
description: ""
category: "database"
tags: [mysql]
---
{% include JB/setup %}

## 1. 问题描述
同事搭了一个Mysql服务，使用的时候发现直接使用mysql命令从命令行操作，插入中文之后再select出来都是正常的，但是通过web服务获取出来就是乱码。  

## 2. 问题分析
首先确认web服务的配置都是正常的，大多都是拷贝的，所以不应该有问题。通过debug也可以确认，是在调用dao之后取得的数据就已经是乱码了。所以着重点就应该是数据库和数据库的操作这一块了。

执行命令[show vairables like "char%";]得到的结果是:  
{% highlight  mysql %}
+--------------------------+----------------------------------+
| Variable_name            | Value                            |
+--------------------------+----------------------------------+
| character_set_client     | latin1                           | 
| character_set_connection | latin1                           | 
| character_set_database   | utf8                             | 
| character_set_filesystem | binary                           | 
| character_set_results    | latin1                           | 
| character_set_server     | utf8                             | 
| character_set_system     | utf8 
{% endhighlight %}
可见，mysql的服务本身的编码已经设置成utf8，但是client和connection还是latin1，所以查询时中文显示为乱码。

## 3. 问题解决
网上查询确认，mysql 5.6版本和以往的版本，在设置编码方面已经不同了。  

### 3.1. 方法一
在确保/etc/my.cnf为如下设置的情况下，在mysql服务所在机器执行命令行，可以看到client和connection都变为utf8了，但是从其他机器连接仍然是latin1，这仍然没有解决问题。

    [mysqld]
    # 省略其他配置
    character_set_server=utf8
    
    [client]
    default-character-set=utf8

### 3.2. 方法二
有人提供了其他方法，在[mysqld]下增加如下配置。测试后发现仍然不起作用。

    init_connect="SET NAMES 'utf8'"

### 3.3. 方法三
通过在连接命令上增加[--default-character-set=utf8]倒是可以生效，但是公司内部的服务框架，数据库连接是不是自定义配置的，所以依然不能解决问题。

### 3.4. 最终解决
只有上官网去找资料解决了。从官网上找到了如下资料。

    It is still necessary for applications to configure their connection using SET NAMES or equivalent after they connect, as described previously. You might be tempted to start the server with the --init_connect="SET NAMES 'utf8'" option to cause SET NAMES to be executed automatically for each client that connects. However, this will yield inconsistent results because the init_connect value is not executed for users who have the SUPER privilege. 

从上面的描述可以知道，方法二是一个有效的方法，但是还需要一定的配置。

问题就这么解决了，新建一个用户，数据是在数据库mysql的表user中。把[Super_priv]的值改成N。
然后无论远程连接还是本机连接都使用这个账号和对应的密码就OK了。

当然，文档里面还有一段话告诉我们，其实在安装mysql的时候指定编码，那么什么问题都不会有了。

    To select a character set and collation when you configure and build MySQL from source, use the DEFAULT_CHARSET and DEFAULT_COLLATION options for CMake.

## 4. 参考资料
4.1. [http://dev.mysql.com/doc/refman/5.6/en/charset-applications.html](10.1.5 Configuring the Character Set and Collation for Applications)
