---
title: GitHub发布Library，compile使用
date: 2018-09-17 00:00:00
categories: misc
tags: github
---

### 项目准备工作

Android Studio创建一个项目，然后新建一个module，选择Android Library，然后把自己的代码贴到新建的module中，原来的app module没什么用了。

<!-- more -->

![new_module](https://raw.githubusercontent.com/blackchy/blackchy.github.io/master/img/new_module.jpg)

![choose_library](https://raw.githubusercontent.com/blackchy/blackchy.github.io/master/img/android_library.jpg)

将项目上推到GitHub远程仓库。

### GitHub操作

打开github上的项目，点击release.

![repository_release](https://raw.githubusercontent.com/blackchy/blackchy.github.io/master/img/repository_release.jpg)

点击Create a new release。

![create_release](https://raw.githubusercontent.com/blackchy/blackchy.github.io/master/img/create_release.jpg)

输入版本号等，然后点击发布。

![release_version](https://raw.githubusercontent.com/blackchy/blackchy.github.io/master/img/release_version.jpg)

### JitPack操作

打开JitPack网站:
[https://jitpack.io/](https://jitpack.io/)
，输入github项目地址，然后点击Look up。

![jitpack_look](https://raw.githubusercontent.com/blackchy/blackchy.github.io/master/img/jitpack_look.jpg)

选择需要发布的版本，然后点击Get it。

![jitpack_git](https://raw.githubusercontent.com/blackchy/blackchy.github.io/master/img/jitpack_git.jpg)

等Get it中的圈圈转完后，会看到如下界面。

![jitpack_result](https://raw.githubusercontent.com/blackchy/blackchy.github.io/master/img/jitpack_result.jpg)

把maven仓库放到项目根目录的build.grale，然后在module的build.gradle添加依赖发布的公共库，就可以愉快地使用了。

### 其他注意事项

1. 如果需要发布后续版本，在github项目重新发布一个（比如v1.1版本等），然后继续走后面的流程就好。
2. 项目相关配置缺失！