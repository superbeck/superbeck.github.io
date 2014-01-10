---
layout: post
title: "用jekyll-boostrap在github上建博客"
description: "如何使用jekyll-bootstrap搭建自己的博客"
category: "github"
tags: [github, git, jekyll, jekyll-bootstrap]
---
{% include JB/setup %}

## 1. 环境准备
### 1.1. 安装git及配置
这是必须的，否则也没办法使用github
网上搜一下就有，要匹配自己的操作系统。可以参考参考资料中内容
### 1.2. 安装jekyll
这不是必须的，因为你写完的markdown文件上传到github服务器上之后，服务器会自动转换成html文件。
但是如果想要在本地调试的话，这个就有必要了。
ubuntu上的安装步骤如下。

```bash
	$ sudo apt-get install rubygems rake
	$ sudo gem install jekyll
```

## 2. 博客搭建步骤
在github上搭建博客有两种。一种是直接创建名为USERNAME.github.io的工程，这样搭建出来的博客是你个人的专属博客，可以直接访问网址http://USERNAME.github.io。另外一种是搭建任意工程如名为myblog，那么访问的网址是http://USERNAME.github.io/myblog。

### 1.1. 搭建个人专属博客步骤

1.1.1. 在github上创建工程: USERNAME.github.io，不创建README文件。

1.1.2. 创建本地jekyll-bootstrap工程并上传到github

    $ git clone git@github.com:plusjade/jekyll-bootstrap.git USERNAME.github.io
    $ cd USERNAME.github.io
    $ git remote set-url origin git@github.com:USERNAME/USERNAME.github.io.git
    $ git push origin master
 (以上命令来源于[http://jekyllbootstrap.com/](http://jekyllbootstrap.com/)。)

1.1.3. 过几分钟等github帮你生成静态html之后就可以访问http://USERNAME.github.io 了。

### 1.2. 搭建某工程博客

1.2.1. 在github上创建工程: myblog，不创建README文件。

1.2.2. 创建本地jekyll-bootstrap工程并上传到github
    
    $ git clone git@github.com:plusjade/jekyll-bootstrap.git myblog
    $ cd myblog
    $ rm -rf .git
    $ git init
    $ git checkout --orphan gh-pages
修改本地文件_config.yml中 
    
    BASE_PATH: http://superbeck.github.io/myblog
继续执行命令
    
    $ git add .
    $ git commit -m "first post"
    $ git remote add origin git@github.com:USERNAME/myblog.git
    $ git push origin gh-pages

1.2.3. 过几分钟访问地址http://USERNAME.github.io/myblog 即可。

## 3. 本地调试
在你的工程目录下执行以下命令即可启动服务:

	$ jekyll serve
然后访问http://localhost:4000就可以看到页面了。
## 4. 参考资料
1. [http://jekyllbootstrap.com/](http://jekyllbootstrap.com/)
2. [http://jekyllrb.com/](http://jekyllrb.com/)
3. [github Pages](http://pages.github.com/)
4. [搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)
5. [Git与Github配置笔记](http://blog.csdn.net/poisonchry/article/details/7706507)
6. [Git历险记（二）——Git的安装和配置](http://www.infoq.com/cn/news/2011/01/git-adventures-install-config)
7. [GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown)
