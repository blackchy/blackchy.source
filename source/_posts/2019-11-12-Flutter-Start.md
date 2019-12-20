title: 我的Flutter入门学习
date: 2019-11-12 00:00:00
categories: flutter
tags: [android,flutter]
---

刚开始因为工作中并没有用到Flutter，所以对Flutter不太感冒，加之之前React Native带给我的很多不好的开发体验，因此Flutter火了很久都还没有去接触它。但是，看到越来越多的人去用Flutter，还有最近一段时间又比较有空闲，就去开始学习Flutter，最后发现它挺好用的，比React Native要友好很多。
<!--more-->
打开[Flutter中文网][0]，开始学习Flutter。

### 开发环境(windows)

#### 前置要求

* 操作系统：Windows7及以上(64位)
* 磁盘空间:400MB
* 工具:git for windows
* IDE:AndroidStudio3.0及以上(下载安装Flutter和Dart插件)

#### Flutter SDK

由于墙的原因，Flutter官网下载sdk不方便，所以到github下载。
项目路径：[Flutter SDK Github Repository][1]

1. 下载最新发布的zip压缩包，解压到自定义文件夹中(如：E:/FlutterSdk/)。
2. 然后打开cmd或powershell输入命令`flutter doctor`检查看看有什么错误，我这里报了一些错误：一些android licenses没有认证，没有安装AndroidStudio、没有Flutter插件、没有Dart插件、没有连接设备，Flutter和Dart插件在AndroidStudio中已经安装所以不用管，其他几个报错明显也不用管。
3. 最后复制`flutter\bin`的全路径准备配置环境变量(如：E:\FlutterSdk\flutter-1.10.15\bin)。

#### 配置环境变量

添加用户变量：

* 新增变量 变量名：PUB_HOSTED_URL 值：https://pub.flutter-io.cn
* 新增变量 变量命：FLUTTER_STORAGE_BASE_URL 值：https://storage.flutter-io.cn
* 找到path变量，在后面添加Flutter SDK的变量，值：`flutter\bin`的全路径(如：E:\FlutterSdk\flutter-1.10.15\bin，记得前面加`;`隔开)

### 开发

#### 新建项目

1. 选择File>New Flutter Project。
2. 选择Flutter application作为项目类型。
3. 输入项目名称。
4. 在`lib/main.dart`文件中开始写代码。

#### 其他事项

1. 模拟器安装后因为cpu类型可能导致安装后无法使用，需要在`android`模块修改gradle配置。
2. 修改app图标、app名称等，其操作和android开发一样，在`android`模块AndroidManifest进行修改。
3. 添加依赖包是在根目录`pubspec.yaml`中添加，然后点击文件上方`Packages get`进行下载构建。
4. 使用项目文件夹里的图片需要在`pubspec.yaml`资源`assets`处加上该文件名称路径，跟android注册activity类似。
5. 修改了dart文件过后点击run图标旁边的闪电图标(或ctrl+s保存)即可进行热重载，直接刷新连接设备的界面。
......

其他就是Flutter UI、组件、框架等的熟悉使用。

[0]:https://flutterchina.club/setup-windows/
[1]:https://github.com/flutter/flutter/releases