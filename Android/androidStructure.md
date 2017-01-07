---
title: Android体系与系统架构
date: 2016-04-27 19:10:17
tags: Android
---
这是一张讲解Android系统架构的经典示意图。它将Android大致分为四层，即Linux内核层、库和运行时、Framework层和应用层。


![Android系统架构](http://dev.icybear.net/learning-android-cn/images/02-AndroidStack.png)

### Linux

Linux层，Android最底层最核心的地方。Linux层包含了Android系统的核心服务，包括硬件驱动、进程管理、安全系统，等等。


### Dalvik与ART

Dalvik包含了一整套的Android运行环境虚拟机，每个App都会分配Dalvik虚拟机来保证互相之间不受干扰，并保持独立。它的特点是在在运行时编译。而从Android 5.x版本开始，ART模式已经取代了Davik，ART采用的是安装时编译。

### Standard libraries

开发者在开源环境中可以使用的开发库。

### Application

不管是使用NDK或者SDK开发的应用，它们都有Android Manifest文件、Dalvik Classes、Resource Bundle这几个东西。

## Android App 组件架构

Android的App组件架构，通常就是Android的四大组件，指的是Activity、BroadCastReceiever、ContentProvider和Service。

### 应用运行上下文对象

Activity、Service、Application都是继承自Context。

Android应用程序在如下所示的几个时间点创建Context。
* 创建Application
* 创建Activity
* 创建Service

当应用程序第一次启动的时候，Android系统都会创建一个Application对象，同时创建Application Context，所有的组件都共同拥有这样一个Context对象，这个应用上下文对象贯穿整个应用进程的生命周期，为应用全局提供了功能和环境支持。而创建Activity和Service组件时，系统也会为它们提供运行的上下文环境，即创建Activity实例、Service实例的Context。可以通过getApplicationContext()方法获取整个App的Context。

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
