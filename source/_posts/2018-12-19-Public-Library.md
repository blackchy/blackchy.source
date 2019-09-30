---
title: GitHub发布Library，compile使用
date: 2018-12-19 00:00:00
categories: misc
tags: github
---
把自己习惯使用的工具类放到一个公共库中并在Github上发布，然后在AndroidStudio的项目中依赖使用，这是一件很方便的事。
<!-- more -->

### 项目相关
1. 本地创建好项目之后，删掉多余无用的东西，然后新建一个Module(Android Library)，把代码写在这个module中，原来的app module就没什么用了。
2. 在项目根目录的build.gradle中添加：
```
classpath 'com.github.dcendents:android-maven-gradle-plugin:1.5'
```
  如图所示：
![img][1]

3. 在新建的module目录的build.gradle中添加(group:自己的github号)：
```
apply plugin: 'com.github.dcendents.android-maven'
group = 'com.github.blackchy'
```
  如图所示：
![img][2]
  并把android里面的无用配置和dependencies里面的无用依赖全部删掉。

### Github相关
1. 在Github新建一个远程仓库，然后把本地仓库上传同步到远程仓库中。
2. 去github上发布这个公共库的代码。
![img][3]
![img][4]
![img][5]
![img][6]

### 发布依赖
这里通过[JitPack][7]发布依赖。
1. 打开[JitPack][7]的首页，并登录(github账号登录)。
2. 把代码发布好的github仓库地址复制到输入框并点击Look up按钮。
![img][8]
![img][9]
  等Log的圈圈转完以后，表示发布完成其他项目已经可以依赖使用这个公共库了，点击Get it按钮跳转到具体依赖方法。
![img][10]

----
**完**

[1]:/uploads/public_library/root_gradle.png
[2]:/uploads/public_library/module_gradle.png
[3]:/uploads/public_library/github_1.png
[4]:/uploads/public_library/github_2.png
[5]:/uploads/public_library/github_3.png
[6]:/uploads/public_library/github_4.png
[7]:https://jitpack.io/
[8]:/uploads/public_library/jitpack_1.png
[9]:/uploads/public_library/jitpack_2.png
[10]:/uploads/public_library/jitpack_3.png