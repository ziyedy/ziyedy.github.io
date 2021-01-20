---
title: Android开发一：HelloWorld
date: 2020-08-29 10:13:02
tags:
	- Android
categories:
	- 编程相关
	- Android
fileName: android-dev-helloworld
---

## HelloWorld程序运行

### AS设置

#### gradle版本设置

在`gradle-wrapper.properties`文件中修改以下代码中的版本号即可

```
distributionUrl=https\://services.gradle.org/distributions/gradle-6.1.1-all.zip
```

同时，可以去gradle官网上下载相关版本，避免下载缓慢的问题

#### 使用镜像

在`build.gradle`文件中的仓库前加上阿里镜像加快下载速度（两处都要进行更改）

```
repositories {
    maven{ url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    google()
    jcenter()
}
```

### 运行程序

在AS设置正常的情况下，直接按运行健即可运行IDE自动创建的helloworld程序了，此时需要先在右上角的`AVD Manager`中创建虚拟机器，之后按运行按钮即可运行，运行效果如下

<img src="http://cdn.ziyedy.top/%E4%B8%80%E3%80%81Android%E5%BC%80%E5%8F%91HelloWorld/helloworld.png" style="zoom:50%;" />

## Android项目结构

将项目结构模式设置为**Project**后（默认为Android），项目结构如下。

其中重点需要关注的是**app**文件，项目中的代码、资源等内容基本都在该文件夹下。而其他文件多半是自动生成的配置文件，一般情况下不需要进行修改

![](http://cdn.ziyedy.top/%E4%B8%80%E3%80%81Android%E5%BC%80%E5%8F%91HelloWorld/%E9%A1%B9%E7%9B%AE%E7%BB%93%E6%9E%84.png)

### app文件下的文件

#### build

包含了一些在编译时自动生成的文件，一般情况下不需要修改

#### libs

项目中需要用到的jar包都放置在该目录下

#### androidTest

用来编写Android Test测试用例的，可以对项目进行一些自动化测试

#### java

放置Java代码的文件夹

#### res

项目中用到的所有图片、布局等资源都存放在该目录下（如：**图片放在drawable下，布局放在layout下，字符串放在values下**）

#### AndroidManifest.xml

整个Android项目的配置文件，在程序中定义的四大组件都需要在这个文件中注册，也可以在该文件中给应用程序添加权限声明。

如下是该文件中的部分代码，该段代码表示对MainActivity这个活动进行注册（未在该文件中进行注册的活动是不能使用的）

```
<activity android:name=".MainActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />	<!--表示该活动是主活动-->

        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

#### test

用来编写单元测试用例

### 主活动代码

由于Android讲究**逻辑和视图分离**，因此应该在**布局文件中编写界面，在活动中引入进来**。

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);	// 引入了一个布局
    }
}
```

因此，在`res/layout`中能够找到helloworld程序的布局样式如下

![](http://cdn.ziyedy.top/%E4%B8%80%E3%80%81Android%E5%BC%80%E5%8F%91HelloWorld/%E5%B8%83%E5%B1%80.png)



### res中的资源文件

在之前已经简单叙述过了，现在简单讲讲如何使用这些资源

以`res/values/strings.xml`为例，该处定义了一个应用程序名的字符串

```
<resources>
    <string name="app_name">My Application</string>
</resources>
```

> **在代码中通过`R.string.app_name`来引用**
>
> **在XML中通过`@string/app_name`来引用**

如AndroidManifest.xml中部分代码如下

```
android:icon="@mipmap/ic_launcher"
android:label="@string/app_name"
android:roundIcon="@mipmap/ic_launcher_round"
```



### build.gradle文件

整个项目结构中有两个这个文件，一个是在项目最外层（之前添加阿里镜像就是在这儿添加的），一个则是在app文件夹下面

#### 外层build.gradle

其中**jcenter()**声明了一个代码托管仓库，很多开源项目都会选择将代码托管在jcenter中。

之后**classpath**声明了一个Gradle插件，该插件表示是使用Gradle来构建Android项目

```
buildscript {
    repositories {
        maven{ url 'http://maven.aliyun.com/nexus/content/groups/public/'}
        google()
        jcenter()
    }
    dependencies {
        classpath "com.android.tools.build:gradle:4.0.1"

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
```

#### app文件下的build.gradle

```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 30
    buildToolsVersion "30.0.2"

    defaultConfig {
        applicationId "com.example.myapplication"
        minSdkVersion 15
        targetSdkVersion 30
        versionCode 1
        versionName "1.0"

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    implementation fileTree(dir: "libs", include: ["*.jar"])
    implementation 'androidx.appcompat:appcompat:1.2.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.0.1'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test.ext:junit:1.1.2'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.3.0'

}
```

**apply plugin**：

> 应用了一个插件，通常是com.android.application与com.android.library二选一。
>
> 前者表示一个应用程序模块，后者表示一个库模块（不能直接运行）

**android**

> 配置项目构建的各种属性

**dependencies**

> 指定项目所有依赖关系：
>
> 1、本地依赖
>
> 2、库依赖
>
> 3、远程依赖：对jcenter库上的开源项目添加依赖关系



## 日志工具





### 参考

第一行代码——Android（郭霖）