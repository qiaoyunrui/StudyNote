# Android 体系与系统架构

这是一张讲解Android系统架构的经典示意图。它将Android大致分为四层，即Linux内核层、库和运行时、Framework层和应用层。

![Android系统架构](http://dev.icybear.net/learning-android-cn/images/02-AndroidStack.png)

---
## Android系统架构

### Linux kernel

Android系统基于Linux内核的，这一层为Android设备的各种硬件提供了底层驱动，如显示驱动、音频驱动、蓝牙驱动、Wifi驱动等等。

### Libraries（系统运行时库）

这一层通过一些C/C++库为Android系统提供了主要的特性支持。如SQLite库提供了数据库的支持，OpenGL\ES库提供了3D绘图的支持，WebKit库提供了浏览器内核的支持等。

在这一层还有Android的运行时库（Android runtime），它主要提供了一些核心库，能够允许开发者使用Java编写Android应用。另外，Android运行时库中还包含了Dalvik虚拟机（5.0系统之后改为ART运行环境），它使得每个Android应用能运行在独立的**进程**中，并且拥有一个自己的虚拟机实例。相较于Java虚拟机，Dalvik是专门为移动设备定制的，它针对手机内存、CPU性能等有限的资源做了优化处理。

Dalvik的特点是在运行时编译，而ART采用的是安装时编译。

### Application Framework（应用框架层）

开发者在开源环境中可以使用的开发库，提供了构建应用程序时可能用到的各种API。

### Applications（应用层）

不管是使用NDK或者SDK开发的应用，它们都有Android Manifest文件、Dalvik Classes、Resource Bundle这几个东西。

---
## Android App 组件架构

Android的App组件架构，通常就是Android的四大组件，指的是Activity、BroadCastReceiever、ContentProvider和Service。

### Context（应用运行上下文对象）

Activity、Service、Application都是继承自Context。

Android应用程序在如下所示的几个时间点创建Context。
* 创建Application
* 创建Activity
* 创建Service

当应用程序第一次启动的时候，Android系统都会创建一个Application对象，同时创建Application Context，**所有的组件都共同拥有这样一个Context对象**，这个应用上下文对象贯穿整个应用进程的生命周期，为应用全局提供了功能和环境支持。而创建Activity和Service组件时，系统也会为它们提供运行的上下文环境，即创建Activity实例、Service实例的Context。可以通过getApplicationContext()方法获取整个App的Context。

---
## Android系统目录

* /system/app/  这里面放一些系统的App
* /system/bin/  这里面放置Linux自带的组件
* /system/build.prop/   这里面记录的是系统的属性消息
* /system/fonts/   系统字体存放目录
* /system/framework/    系统的核心文件、框架层
* /system/lib/  存放几乎所有的共享库(.so)文件
* /system/media/    该目录用来保存系统提示音、系统铃声
* /system/usr/    该目录用来保存用户的配置文件，如键盘布局、共享、时区文件等
* /data/app/  data目录包含了用户的大部分数据信息。
* /data/data/   包含了App的数据信息、文件信息、数据库信息，以包名的方式区分各个应用。
* /data/system/   包含了手机的各项系统消息
* /data/misc/   保存了大部分Wi-Fi、VPN信息
