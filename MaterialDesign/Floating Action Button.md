# Floating Action Button

## 介绍

* 见名知其意，这是一个悬浮按钮。

* 因为悬浮，所以会有阴影效果。

* 颜色一般为APP主题色中的colorAccent（即```@color/colorAccent```）。

## 使用场景

  一个界面上最主要的操作，一个界面一般情况下最多有一个FAB。

  * View的右下角

  ![pic](http://prolificinteractive.com/blog/wp-content/uploads/2014/07/fab_example_mail1.png)

---
  * 两个View的交界处

  ![pic](https://divshot.com/assets/blog/polymer-material-design/fab-4e166006b5e6db98a93a936527efa426.jpg)

---
  * 一个View的右下角，点击展开更多小的Fab

  ![fab](http://materialdesignblog.com/wp-content/uploads/2015/02/1423852761292.png)

  ![fab](http://materialdesignblog.com/wp-content/uploads/2015/02/ezgif-264169158.gif)

---
<font  color="#fff000" size="6px">所有FAB所代表的操作都是最重要的操作。</font>

## 依赖

```java
dependencies {
    compile 'com.android.support:design:25.0.0' //最新
}
```

## XML属性

* android:src - 设置FAB中心显示的drawable

* android:background - 设置FAB的显示颜色

* app:fabSize - 设置FAB的显示大小，有三个属性可以选：normal、mini、auto

* app:rippleColor - FAB按下去的颜色

* app:borderWidth - FAB的边框宽度

* app:elevation - 设置正常状态下FAB的景深（即Z轴的高度），默认是6dp

* app:pressedTranslationZ - 设置FAB按下时的景深，默认是12dp

* app:layout_anchor - 设置FAB的锚点，即以哪个控件为參照点设置位置

* app:layout_anchorGravity - 设置FAB相对锚点的位置，值有 bottom、center、right、left、top等

<font color="#FF4081" size="5px">注意：layout_anchor和layout_anchorGravity只有在父控件是CoordinatorLayout才是有效的。</font>

## 动画

### 最简单的

* 显示

```java
mFabTest.show();
```

* 隐藏

```java
mFabTest.hide();
```

### 进阶 - 更加炫酷的动画
<br/>
<img src="http://llzz.me/2015/08/28/Android-Support-Design-Library-FAB/fab_toolbar.gif" height="300dp" alt="preview"/>
<img src="http://llzz.me/2015/08/28/Android-Support-Design-Library-FAB/fab_sheet.gif" height="300dp" alt="preview"/>
<img src="http://llzz.me/2015/08/28/Android-Support-Design-Library-FAB/fab_layout.gif" height="300dp" alt="preview"/>
<br/>

---

想要实现这种动画需要使用第三方库：[fab-transformation](https://github.com/konifar/fab-transformation)

1. 添加依赖
```java
dependencies {
    compile 'konifar:fab-transformation:1.0.0'
}
```

2. 把FAB和要转化的View放在布局中

3. 用RevealFrameLayout把转化的View包起来

4. 代码实现转化过程
  * FAB变为View
```java
FabTransformation.with(fab)
                 .transformTo(toolbarFooter);
```

  * View转化为FAB
```java
FabTransformation.with(fab)
                 .transformFrom(toolbarFooter);
```

  * 更多
```java
FabTransformation.with(fab)
                 .overlay(overlayView)
                 .duration(500)
                 .setListener(new FabTransformation.OnTransformListener() {
                                @Override
                                public void onStartTransform() {
                                    //
                                }

                                @Override
                                public void onEndTransform() {
                                    //
                                }
                 })
                 .transformFrom(toolbarFooter);
```

## END & THANK

---
**注：以上所有图片均来自于互联网，若有侵权，立即删除。**
