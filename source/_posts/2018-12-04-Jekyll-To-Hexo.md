---
title: 博客-Jekyll迁移到Hexo
date: 2018-12-04 00:00:01
categories: misc
tags: [jekyll,hexo]
---

发现了比[Jekyll][0]用起来更舒服的[Hexo][1]，它是一个快速、简洁且高效的博客框架，其中使用Markdown（或其他渲染引擎）解析文章，最后生成静态网页。研究了一下[Hexo][1]的[文档][2]过后马上就把它应用到了博客中。
<!-- more -->

详细流程(Windows环境)如下，具体可参考[Hexo文档][2]。

### 环境准备

1. 安装Git、安装Node.js
 * [Git][3]
 * [Node.js][4]

2. 安装Hexo 

```
$ npm install -g hexo-cli
```
### 建站

```
$ hexo init blackchy.github.io
$ cd blackchy.github.io
$ npm install
```
### 配置

打开根目录下的`_config.yml`文件，主要配置以下内容。
```
# Site
title: Black Chy's Blog
subtitle: 香蕉是最受欢迎的水果。
description: Just a dog.
keywords: Android, Developer, Dog
author: Black Chy
language: zh-CN
timezone: Asia/Chongqing
```

### 主题

项目默认是landscape主题，这里修改为[hexo-theme-next][5]。
首先下载next主题到项目中。
```
$ git clone https://github.com/theme-next/hexo-theme-next themes/next
```
然后把根目录下`_config.yml`文件中的`theme`改为`next`。next主题需要手动生成`tags`和`categories`，并修改index文件中的内容:
```
$ hexo new page tags
$ hexo new page categories
```
```
---
title: 所有标签
type: tags
date: 2018-12-04 00:00:00
---
```
```
---
title: 所有分类
type: categories
date: 2018-12-04 00:00:00
---
```
最后，在下载好的next主题中也有一个`_config.yml`文件（路径：`theme->next->_config.yml`），打开进行next主题相关配置。例如：
1. next内部又有4个不同的设计风格，这里使用的是Gemini
``` 
scheme: Gemini
 ```
2. 设置头像
```
avatar:
  #头像url
  url: /uploads/avatar.png
  #圆头像
  rounded: true
  #透明度 0-1
  opacity: 1
  #旋转动画
  rotated: true
```
3. 设置菜单列表，仅打开 首页、关于、归档，其他全部关闭。
```
menu:
  home: / || home
  about: /about/ || user
  #tags: /tags/ || tags
  #categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

4. 打开不蒜子网页计数器
```
busuanzi_count:
  enable: true
  total_visitors: true
  total_visitors_icon: user
  total_views: true
  total_views_icon: eye
  post_views: true
  post_views_icon: eye
```

5. 安装其他js
```
fastclick: true
$ cd themes/next
$ git clone https://github.com/theme-next/theme-next-fastclick source/lib/fastclick

lazyload: true
$ cd themes/next
$ git clone https://github.com/theme-next/theme-next-jquery-lazyload source/lib/jquery_lazyload

pace: true
$ cd themes/next
$ git clone https://github.com/theme-next/theme-next-pace source/lib/pace
```

等等......

### 添加搜索功能

1. 安装插件
```
npm install hexo-generator-search
npm install hexo-generator-searchdb
```
2. 修改hexo配置
根目录下`_config.yml`中增加如下配置:
```
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```
3. 配置next中的搜索入口
打开`themes/next/_config.yml`，修改配置:
```
local_search:
  enable: true
  trigger: auto
  top_n_per_article: 1
```

参考链接：[为hexo和next增加站内搜索功能][6]

### 迁移

把Jekyll目录下的_posts中文章全部复制到Hexo目录下的source/_posts中，并把文章名统一为`:year-:month-:day-:title.md`，需要注意的是` _ `开头的文章不会显示，最后修改文章参数：

```
---
title: blog title
date: YYYY-MM-DD HH:mm:ss 
categories: misc
tags: [tag1, tag2]
---
```
文章太长则需要手动添加`<!-- more -->`以显示`阅读全文`。

### 部署 (Git)

部署前可以先本地运行试看效果，在根目录运行服务器命令：
```
$hexo server 简写 hexo s
```
然后打开浏览器访问查看博客（默认：http://localhost:4000 ）

本地试运行的博客没有问题后就可以去部署到Github上了。

1. 本地配置deploy,打开根目录`_config.yml`。
```
deploy:
  - type: git
    repo: git@github.com:blackchy/blackchy.github.io.git
    branch: [master]
    message: a commit message
```
2. 安装hexo-deployer-git
```
$ npm install hexo-deployer-git --save
```
3. 执行部署
```
$ hexo g -d
```

### 注意事项

* 由于Jekyll上传github服务器会自动生成并部署，而Hexo需要本地生成静态文件再上传，所以需要另起一个分支存放hexo源码。
* 通过git克隆下来的theme、js，删除对应的.git文件夹。

----

<b>至此，迁移完毕。</b>

[0]:https://jekyllrb.com/
[1]:https://github.com/hexojs/hexo
[2]:https://hexo.io/zh-cn/docs/
[3]:https://git-scm.com/
[4]:https://nodejs.org/
[5]:https://github.com/theme-next/hexo-theme-next
[6]:https://www.jianshu.com/p/5b62c01c4dfa