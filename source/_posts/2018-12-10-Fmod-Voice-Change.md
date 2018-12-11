---
title: fmod变声方法简单调用
date: 2018-12-10 00:00:01
categories: android
tags: audio
---

[fmod][1]是一个强大的声音引擎框架，QQ、魔兽世界及其他很多游戏都是使用的这套框架，框架内包含几十种声音类型，还可以修改声音的频率、速度等等。下面来简单调用一下[fmod][1]的变声方法。
<!-- more -->

### 下载 fmod API

进入[fmod download][2]下载Android API。

![img][3]

### 新建项目

勾选include c/c++ support。

![img][4]

勾选异常检测和安全转换

![img][5]

### 加入API

打开下载之后解压的fmod API目录，到`api/lowlevel`文件夹，把`inc`文件夹及里面的文件全部复制到项目的`app/src/main/cpp`文件夹中,把`lib`文件夹中的所有文件夹和文件复制到项目的`app/libs`文件夹中，jar包要右键`add as library`。

![img][6]

### 配置app/build.gradle

android/defaultConfig里新增：
```
ndk {
  abiFilters "armeabi","arm64-v8a","armeabi-v7a","x86"
}
```
android里新增：
```
sourceSets.main {
  jniLibs.srcDirs = ['libs']
  jni.srcDirs = []
}
```
![img][7]

### 配置app/CMakeLists.txt
```
cmake_minimum_required(VERSION 3.4.1)

#-----------------------------------------
find_library( log-lib
              log )

set(my_lib_path ${CMAKE_SOURCE_DIR}/libs)

# 添加三方的so库
add_library(libfmod
            SHARED
            IMPORTED )

 # 指名第三方库的绝对路径
 set_target_properties( libfmod
                        PROPERTIES IMPORTED_LOCATION
                        ${my_lib_path}/${ANDROID_ABI}/libfmod.so )

 add_library(libfmodL
             SHARED
             IMPORTED )

 set_target_properties( libfmodL
                        PROPERTIES IMPORTED_LOCATION
                        ${my_lib_path}/${ANDROID_ABI}/libfmodL.so )

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11")

#--------------------------------
add_library( # Sets the name of the library.
             changeVoice

             # Sets the library as a shared library.
             SHARED

             # Provides a relative path to your source file(s).
             src/main/cpp/native-lib.cpp )

#---------------------
# 导入路径，为了让编译时能够寻找到这个文件夹
include_directories(src/main/cpp/inc)

# 链接好三个路径
target_link_libraries( changeVoice
                       libfmod
                       libfmodL

                       ${log-lib} )
```

### 代码部分

布局文件：
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFF"
    android:gravity="center_horizontal"
    android:orientation="vertical"
    android:padding="10pt"
    >

  <LinearLayout
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:orientation="horizontal"
      >

    <Button
        android:id="@+id/btn_1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="原声"
        />

    <Button
        android:id="@+id/btn_2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="萝莉"
        />


    <Button
        android:id="@+id/btn_3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="大叔"
        />
  </LinearLayout>

  <LinearLayout
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:orientation="horizontal"
      >

    <Button
        android:id="@+id/btn_4"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="惊悚"
        />

    <Button
        android:id="@+id/btn_5"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="搞怪"
        />

    <Button
        android:id="@+id/btn_6"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="空灵"
        />
  </LinearLayout>
</LinearLayout>
```
java代码，新建Util类：
```
public class Util {

  //音效的类型
  public static final int MODE_NORMAL = 0;//正常
  public static final int MODE_LUOLI = 1;//萝莉
  public static final int MODE_DASHU = 2;//大叔
  public static final int MODE_JINGSONG = 3;//惊悚
  public static final int MODE_GAOGUAI = 4;//搞怪
  public static final int MODE_KONGLING = 5;//空灵

  /**
   * 变声
   *
   * @param path 声音路径
   * @param type 变声类型
   */
  public static native void voiceChange(String path, int type);

  static {
    System.loadLibrary("fmodL");
    System.loadLibrary("fmod");
    System.loadLibrary("changeVoice");
  }
}
```
java代码，MainActivity类：
```
public class MainActivity extends AppCompatActivity {

  @Override protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    FMOD.init(this);
    setContentView(R.layout.activity_main);
    findViewById(R.id.btn_1).setOnClickListener(new View.OnClickListener() {
      @Override public void onClick(View v) {
        VoiceFun1(v);
      }
    });
    findViewById(R.id.btn_2).setOnClickListener(new View.OnClickListener() {
      @Override public void onClick(View v) {
        VoiceFun2(v);
      }
    });
    findViewById(R.id.btn_3).setOnClickListener(new View.OnClickListener() {
      @Override public void onClick(View v) {
        VoiceFun3(v);
      }
    });
    findViewById(R.id.btn_4).setOnClickListener(new View.OnClickListener() {
      @Override public void onClick(View v) {
        VoiceFun4(v);
      }
    });
    findViewById(R.id.btn_5).setOnClickListener(new View.OnClickListener() {
      @Override public void onClick(View v) {
        VoiceFun5(v);
      }
    });
    findViewById(R.id.btn_6).setOnClickListener(new View.OnClickListener() {
      @Override public void onClick(View v) {
        VoiceFun6(v);
      }
    });
  }

  public void VoiceFun1(View btn) {
    String path = Environment.getExternalStorageDirectory().getAbsolutePath()
        + File.separatorChar
        + "/Download/10028.wav";
    File file = new File(path);
    if (file.exists()) {
      Toast.makeText(this, "变声-正常", Toast.LENGTH_SHORT).show();
      Util.voiceChange(path, Util.MODE_NORMAL);
      Log.e("x---", "VoiceFun1");
    } else {
      Log.e("x---", "file is not exist");
      Toast.makeText(this, "file is not exist", Toast.LENGTH_SHORT).show();
    }
  }

  public void VoiceFun2(View btn) {
    String path = Environment.getExternalStorageDirectory().getAbsolutePath()
        + File.separatorChar
        + "/Download/10028.wav";
    File file = new File(path);
    if (file.exists()) {
      Toast.makeText(this, "变声-萝莉", Toast.LENGTH_SHORT).show();
      Util.voiceChange(path, Util.MODE_LUOLI);
      Log.d("jason", "VoiceFun2");
    } else {
      Log.e("x---", "file is not exist");
      Toast.makeText(this, "file is not exist", Toast.LENGTH_SHORT).show();
    }
  }

  public void VoiceFun3(View btn) {
    String path = Environment.getExternalStorageDirectory().getAbsolutePath()
        + File.separatorChar
        + "/Download/10028.wav";
    File file = new File(path);
    if (file.exists()) {
      Toast.makeText(this, "变声-大叔", Toast.LENGTH_SHORT).show();
      Util.voiceChange(path, Util.MODE_DASHU);
      Log.e("x---", "VoiceFun3");
    } else {
      Log.e("x---", "file is not exist");
      Toast.makeText(this, "file is not exist", Toast.LENGTH_SHORT).show();
    }
  }

  public void VoiceFun4(View btn) {
    String path = Environment.getExternalStorageDirectory().getAbsolutePath()
        + File.separatorChar
        + "/Download/10028.wav";
    File file = new File(path);
    if (file.exists()) {
      Toast.makeText(this, "变声-惊悚", Toast.LENGTH_SHORT).show();
      Util.voiceChange(path, Util.MODE_JINGSONG);
      Log.e("x---", "VoiceFun4");
    } else {
      Log.e("x---", "file is not exist");
      Toast.makeText(this, "file is not exist", Toast.LENGTH_SHORT).show();
    }
  }

  public void VoiceFun5(View btn) {
    String path = Environment.getExternalStorageDirectory().getAbsolutePath()
        + File.separatorChar
        + "/Download/10028.wav";
    File file = new File(path);
    if (file.exists()) {
      Toast.makeText(this, "变声-搞笑", Toast.LENGTH_SHORT).show();
      Util.voiceChange(path, Util.MODE_GAOGUAI);
      Log.e("x---", "VoiceFun5");
    } else {
      Log.e("x---", "file is not exist");
      Toast.makeText(this, "file is not exist", Toast.LENGTH_SHORT).show();
    }
  }

  public void VoiceFun6(View btn) {
    String path = Environment.getExternalStorageDirectory().getAbsolutePath()
        + File.separatorChar
        + "/Download/10028.wav";
    File file = new File(path);
    if (file.exists()) {
      Toast.makeText(this, "变声-空灵", Toast.LENGTH_SHORT).show();
      Util.voiceChange(path, Util.MODE_KONGLING);
      Log.e("x---", "VoiceFun6");
    } else {
      Log.e("x---", "file is not exist");
      Toast.makeText(this, "file is not exist", Toast.LENGTH_SHORT).show();
    }
  }

  @Override protected void onDestroy() {
    super.onDestroy();
    FMOD.close();
  }

  @Override public void onBackPressed() {
    super.onBackPressed();
    finish();
  }
}
```
C++代码，app/src/main/cpp/native-lib.cpp:
```
#include <jni.h>
#include "inc/fmod.hpp"
#include <string>

#include <unistd.h>

using namespace FMOD;

#define MODE_NORMAL 0
#define MODE_LUOLI 1
#define MODE_DASHU 2
#define MODE_JINGSONG 3
#define MODE_GAOGUAI 4
#define MODE_KONGLING 5

#include <android/log.h>

#define LOGI(FORMAT, ...) __android_log_print(ANDROID_LOG_INFO,"voiceChange",FORMAT,##__VA_ARGS__);
#define LOGE(FORMAT, ...) __android_log_print(ANDROID_LOG_ERROR,"voiceChange",FORMAT,##__VA_ARGS__);

extern "C"
JNIEXPORT void JNICALL
Java_com_exp_fmodvoicechange_Util_voiceChange(JNIEnv *env, jclass cls, jstring path_str, jint type) {
    System * system;
    Sound * sound;
    Channel  *channel;
    DSP *dsp;
    bool playing= true;
    float frequency=1;

    //初始化
    System_Create(&system);
    system->init(32, FMOD_INIT_NORMAL, NULL);

    //将string转成char*
    const char* path=env->GetStringUTFChars(path_str,NULL);

    //创建声音
    system->createSound(path,FMOD_DEFAULT, 0, &sound);
    try {
        //根据类型改变声音
        switch (type) {
            case MODE_NORMAL:
                //正常声音
                system->playSound(sound, 0, false, &channel);
                break;
            case MODE_LUOLI:
                //萝莉
                //DSP digital signal process
                //dsp -> 音效 创建fmod中预定义好的音效
                //FMOD_DSP_TYPE_PITCHSHIFT dsp，提升或者降低音调用的一种音效
                system->createDSPByType(FMOD_DSP_TYPE_PITCHSHIFT, &dsp);
                //提高音效
                dsp->setParameterFloat(FMOD_DSP_TYPE_PITCHSHIFT, 2.5);
                //播放声音
                system->playSound(sound, 0, false, &channel);
                //将channel添加到dsp
                channel->addDSP(0, dsp);
                break;
            case MODE_DASHU:
                //大叔
                system->createDSPByType(FMOD_DSP_TYPE_PITCHSHIFT, &dsp);
                //降低声音
                dsp->setParameterFloat(FMOD_DSP_PITCHSHIFT_PITCH, 0.8);
                //播放声音
                system->playSound(sound, 0, false, &channel);
                //将channel添加到dsp
                channel->addDSP(0, dsp);
                break;
            case MODE_JINGSONG:
                //惊悚
                system->createDSPByType(FMOD_DSP_TYPE_TREMOLO, &dsp);
                dsp->setParameterFloat(FMOD_DSP_TREMOLO_SKEW, 0.5);
                system->playSound(sound, 0, false, &channel);
                channel->addDSP(0, dsp);
                break;
            case MODE_GAOGUAI:
                //搞怪
                //提高说话的速度
                system->playSound(sound, 0, false, &channel);
                //frequency  原来的声音速度
                channel->getFrequency(&frequency);
                frequency = frequency * 1.6;
                channel->setFrequency(frequency);
                break;
            case MODE_KONGLING:
                //空灵
                system->createDSPByType(FMOD_DSP_TYPE_ECHO, &dsp);
                dsp->setParameterFloat(FMOD_DSP_ECHO_DELAY, 300);
                dsp->setParameterFloat(FMOD_DSP_ECHO_FEEDBACK, 20);
                system->playSound(sound, 0, false, &channel);
                channel->addDSP(0, dsp);
                break;
            default:
                break;
        }
    }
    catch (...){
        //捕捉异常
        LOGE("%s","发生异常");
    }
    system->update();
    //释放资源
    //单位是微秒
    //每秒钟判断下是否在播放
    while(playing){
        channel->isPlaying(&playing);
        usleep(1000 * 1000);
    }

    //释放
    sound->release();
    system->close();
    system->release();
    env->ReleaseStringUTFChars(path_str,path);
}
```
### 最后

* 界面：
![img][8]
* 项目配置：
	1. Android Studio 2.3.3
	2. gradle 2.3.3
	3. sdk version 26
	4. [fmod API version 1.10.10][2]
	5. [ndk version 14][9]
* 权限：
```
<!-- xmlns:tools="http://schemas.android.com/tools" -->
<!--读写sd卡权限-->
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission
      android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"
      tools:ignore="ProtectedPermissions" />
```
* [项目地址][10]

[1]:https://www.fmod.com/
[2]:https://www.fmod.com/download
[3]:/uploads/fmod-voice-change/fmod-download.png
[4]:/uploads/fmod-voice-change/new-project.png
[5]:/uploads/fmod-voice-change/new-project2.png
[6]:/uploads/fmod-voice-change/add-api.png
[7]:/uploads/fmod-voice-change/modify-gradle.png
[8]:/uploads/fmod-voice-change/page.png
[9]:https://developer.android.google.cn/ndk/downloads/older_releases
[10]:https://github.com/blackchy/FmodVoiceChange