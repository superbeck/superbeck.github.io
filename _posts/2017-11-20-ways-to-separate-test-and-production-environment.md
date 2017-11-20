---
layout: post
title: "Spring3.1之前版本分离测试和生产环境的方法"
en-title: "Ways to Separate Test And Production Environment With Pre 3.1 Version Of Spring"
description: ""
category: "Spring"
tags: [Spring, java]
---

* content
{:toc}

## 1. 问题描述
做过一些比较复杂的业务，尤其是与钱有关的业务的话，应该会有这样的需求，就是测试时产生的数据不要影响到线上，这就需要把测试环境和生产环境分离，尤其是数据库。

最简单的办法，也是我们所不希望的，就是每次做环境的时候修改一次代码来实现这个分离。

那么有什么别的好办法吗？
<!--excerpt-->

## 2. Spring 3.1以后版本的方法
使用Spring的话，还是有办法的。Spring3.1以后的版本支持profile，spring的配置文件中通过内嵌的beans节点定义不同的profile使用的bean的定义。
具体的示例如下:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="…">
<beans profile="standalone">  
<bean id="dataSource"> 
	<property name="driverClassName" value="org.hsqldb.jdbcDriver"/> 
	<property name="url" value="jdbc:hsqldb:hsql://localhost"/> 
	<property name="username" value="sa"/> 
	<property name="password" value=""/> 
</bean> 
</beans> 
 
<beans profile="container">
<jee:jndi-lookup id="dataSource" jndi-name="java:mydatasource"/>
</beans>

</beans>
```

启动时选择特定的profile即可。
比如在服务启动时，加jvm参数:
```
-Dspring.profiles.active=standalone
```

## 3. Spring 3.1之前版本的方法
那么旧的版本没有profile怎么办呢？
没关系，我们还可以使用import标签。

首先在spring配置文件中，增加如下配置
```
<import resource="profile-applicationContext-service-${spring.profile}.xml" />
```

然后分别创建对应不同环境的子配置文件，如
生产环境: profile-applicationContext-service-production.xml
测试环境: profile-applicationContext-service-test.xml

那么只需要在启动的时候，加jvm参数-Dspring.profile=test或者-Dspring.profile=production就可以分别启用测试环境或生产环境专用的bean.

## 4. 规则及注意事项
这些规则只针对与Spring 3.1以前版本的方法。

### 4.1. 子配置文件的命名规范
* 以profile开头是加一个特殊的前缀防止跟别的配置文件重名。
* {profile}的名字也要统一，测试环境用test,生产环境用production，如果有其他需求，也可以定下来。
* 使用占位符的import之后,启动参数不加会报错，可以查看log确认。

### 4.2. 使用方法说明
* 如果是某一个方法依赖了不同的实现，那么就定义一个接口，提供两个不同的实现，然后分别配置在两个子配置文件中即可。
* 如果只是某一段代码需要替换成不同的实现的，那么可以使用replace-method配置。

     需要在production配置文件中配置原bean,在生产环境中配置同样的bean,但是要把特定的方法使用replace-method代替。

## 5. 参考资料
1. [如何用Spring 3.1的Environment和Profile简化工作](http://www.importnew.com/1099.html)
