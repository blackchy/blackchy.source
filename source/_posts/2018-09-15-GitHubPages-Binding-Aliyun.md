---
title: GitHub Pages与阿里云域名的绑定
date: 2018-09-15 00:00:01
categories: misc
tags: github
---

搭建好自己的个人博客过后，会发现博客的访问地址是github.io后缀，于是我在阿里云买了一个域名，并把这个域名指向了我的博客服务器地址。
<!-- more -->

### 获取github pages的ip地址

![img][1]

### 配置阿里云域名解析

![img][2]

![img][3]

### 配置github pages的custom domain

进入github pages项目的设置，将解析的域名地址添加到custom domain中并保存。

![img][4]

### 验证

blog : [blackchy's blog][5]

[1]:/uploads/githubpages-binding-aliyun/ping.png
[2]:/uploads/githubpages-binding-aliyun/peiz0.png
[3]:/uploads/githubpages-binding-aliyun/peiz.png
[4]:/uploads/githubpages-binding-aliyun/setting.png
[5]:http://blackchy.com