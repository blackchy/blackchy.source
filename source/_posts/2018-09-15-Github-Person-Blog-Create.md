---
title: Github个人博客创建流程
date: 2018-09-15 00:00:00
categories: misc
tags: github
---

有些有价值的东西我想把它记录下来，所以拥有自己的博客方便记录它们是一件非常令人高兴的事。Github Pages提供了自己搭建服务器的渠道，由Github提供的博客服务器是免费且不限空间的。以下是我在网上慢慢查找的搭建博客的简略流程。
<!-- more -->

###  注册登录github帐号。

[Github][1]

具体注册登录等流程网上搜索，这里不作阐述。

### 创建博客项目

![img][2]

在github新建一个repository,这里的Repository name输入自己github账号的username，比如blackchy，之后会成功创建一个项目`xxx.github.io`，然后`https://xxx.github.io`就是个人博客的网址。

![img][3]

在`xxx.github.io`项目点进Settings，找到GitHub Pages模块，Theme Chooser可以选择一个博客的主题，不过一般都是在jekyll或其他地方寻找合适的主题，下面会简单介绍jekyll。Custom domain可以修改博客地址，如果有自己的个人域名可以修改成自己的域名网址，而不用`https://xxx.github.io`作为博客地址。

### 设计自己的博客网站

可以自己写一个html网站作为博客网站，但是那样对非专业人士、懒人等来说不太友好，所以推荐使用别人写好的模板直接下载下来使用，选择的时候尽量选可塑性较强的模板，以便后面进行修改。jekyll模板地址如下：

[http://jekyllthemes.org/][4]

![img][5]

下载安装桌面github以便对项目进行管理，地址如下：

Windows：
[https://windows.github.com/][6]


Mac：
[https://mac.github.com/][7]


把github上的`xxx.github.io`项目克隆到本地，然后下载选择好的模板。
删除掉本地项目的所有文件和文件夹(.git文件夹除外)，然后把模板的所有文件和文件夹复制到本地项目中，然后commit并推到github远程仓库，大概几秒到1分钟内，刷新`https://xxx.github.io`网页就能看到已经复制过来的网站了。


最后，修改一下模板里面的信息再更新，这就是一个完全属于自己的个人博客了。

注：一般博客都是放在posts这个文件夹，用markdown写的，弄好自己的博客网站之后就可以开心地更新博客了。


[1]:https://github.com/
[2]:/uploads/github-person-blog-create/new_repository.jpg
[3]:/uploads/github-person-blog-create/repository_settings.jpg
[4]:http://jekyllthemes.org/
[5]:/uploads/github-person-blog-create/jekyll_demos.jpg
[6]:https://windows.github.com/
[7]:https://mac.github.com/