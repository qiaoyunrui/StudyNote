---
title: Android控件架构与自定义控件
date: 2016-05-01 17:53:33
tags: Android
---

### Android控件架构

Android中的每个控件都会在界面中占一个矩形的区域，这些控件分为两种：View控件、ViewGroup控件。通过ViewGroup，整个界面上的控件会形成一个控件树，findViewById()方法就是以深度优先算法遍历查找对应控件。在每个控件树的顶部，都有一个ViewParent对象。

Android界面架构图：

![Android界面架构图](http://hujiaweibujidao.github.io/images/androidheros_ui.png)

如图所示，每个Activity都包含一个Window对象，在Android中Window对象通常由PhoneWindow来实现。PhoneWindow将一个DecorView设置为整个应用窗口的根View。DecorView将要显示的内容呈现在PhoneWindow上，所有View的监听事件，都通过**WindowManagerService**来进行比较，并通过Activity来回调对应的onClickListener。在显示屏幕上，将屏幕分成两个部分，一个是TitleView，另一个是ContentView。ContentView是一个ID为content的FrameLayout。

当程序在onCreate()方法中调用setContentView()方法后，**ActivityMananagerService** 会回调onResume()方法，此时系统才会把整个 DecorView 添加到 PhoneWindow中，并让其显示出来，从而完成界面的绘制。

### View的测量

Android系统在绘制View前，必须要对View进行测量，这个过程在onMeasure()方法中进行。

MeasureSpec类可以帮助测量View，MeasureSpec是一个32位的int值，其中高2位为测试的模式，低30位为测量的大小，在计算中使用位运算的原因是为了提高并优化效率。

测量的模式可以为一下3种：
* EXACTLY：精确模式
* AT_MOST：最大值模式
* UNSPECIFIED：不指定大小测量模式，View想多大就多大。

View类默认的onMeasure方法只支持EXACTLY模式，所以如果在自定义控件的时候如果不重写onMeasure()方法的话，就只能使用EXACTLY模式。

在onMeasure()方法中，系统最终会调用setMeasureDimension(int measuredWidth,int measureHeight)方法将测量后的宽高值设置进去。

### View的绘制

想要在Android的界面中绘制相应的图像，就必须在Canvas上进行绘制。通常需要通过继承View并重写 onDraw()方法进行绘制。

```java
@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
}
```

### ViewGroup的测量

ViewGroup会去管理子View，其中一个管理项目就是负责子View的显示大小。

当ViewGroup的大小为wrap_content是，ViewGroup 就需要子 View 进行遍历，以便获取所有子View的大小，从而决定自己的大小。而在其他模式下则会通过其具体的在指定值来设置自身的大小。

当子View测量完毕的时候，就需要将子View放在合适的位置，这个过程就是View的Layout()方法。ViewGroup在执行Layout的过程中，同样是使用遍历的方式调用子View的Layout方法，并指定其具体显示的过程，从而决定其布局位置。
在自定义ViewGroup时，通常会去重写onLayout()方法来控制子View显示位置的逻辑。

### ViewGroup的绘制

ViewGroup会使用dispatchDraw()方法绘制子View，其过程同样是通过遍历所有子View，并调用子View的绘制方法来完成绘制工作。

### 自定义View

在自定义View时，通常会去重写onDraw()方法绘制View的显示内容。如果该View还要使用wrap_content属性，那么还需要重写onMeasure()方法。另外，通过自定义attrs属性，还可以设置新的属性配置值。

View中一些比较重要的回调方法：
* onFinishInflate()：从 XML 加载组件后的回调
* onSizeChanged()：组件大小改变时回调
* onMeasure()：回调该方法进行测量
* onTouchEvent()：监听到触摸事件时回调
* onLayout()：回调该方法来确定显示的位置

通常情况下，有三种方法来实现自定义控件：
* 最先有进行拓展
* 通过组合来实现新的控件
* 重写View来实现全新的控件

#### 事件拦截机制分析

ViewGroup的相关回调函数是onInterceptTouchEvent()、onTouchEvent()、dispatchTouchEvent()。
View的相关回调函数是onTouchEvent()、dispatchTouchEvent()。

触摸事件的传递顺序是：
ViewGroup -> View
事件传递的时候，先执行dispatchTouchEvent()方法，再执行onInterceptTouchEvent()方法。

事件的处理顺序是：
View -> ViewGroup
事件处理都是执行onTouchEvent()方法。

事件传递的返回值：
True -> 拦截，不继续
False -> 不拦截，继续流程

事件处理的返回值：
True -> 处理了，不用交给ViewGroup处理
Flase -> 没有处理，需要ViewGroup处理

初始情况下，返回值都是false。
