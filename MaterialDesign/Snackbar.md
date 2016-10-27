# Snackbar

## 介绍

Snackbar 是一种针对操作的轻量级反馈机制，常以一个小的弹出框的形式，出现在手机屏幕下方或者桌面左下方。它们出现在屏幕所有层的最上方，包括浮动操作按钮。

它们会在超时或者用户在屏幕其他地方触摸之后自动消失。Snackbar 可以在屏幕上滑动关闭。当它们出现时，不会阻碍用户在屏幕上的输入，并且也不支持输入。屏幕上同时最多只能现实一个 Snackbar。

Android 也提供了一种主要用于提示系统消息的胶囊状的提示框 Toast。Toast 同 Snackbar 非常相似，但是 Toast 并不包含操作也不能从屏幕上滑动关闭。

* Toast的加强版

* 可以设置一个按钮

* 配合CoordinatorLayout有更多有趣的效果

  ![preview](http://wiki.jikexueyuan.com/project/material-design/images/components-toasts-usage-spec_toast_do_24_large_mdpi.png)

## 依赖

```java
dependencies {
    compile 'com.android.support:design:25.0.0' //最新
}
```

## 使用方法

```java
Snackbar snackbar = Snackbar.make(coordinatorLayout, "Snack Bar", Snackbar.LENGTH_SHORT);
//设置按钮的监听事件
snackbar.setAction("Log", new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Log.i(TAG,"Snackbar click");
    }
});
snackbar.show();
```

## 规格

手机端 Snackbar（默认就可以了）

* 单行 Snackbar 高度：48 dp
* 多行 Snackbar 高度：80 dp
* 文本：Roboto Regular 14 sp
* 操作按钮：Roboto Medium 14 sp, 大写文本
* 默认背景填充色：#323232 100%

## 注意事项

1. make()方法的第一个参数（View）如果是CoordinatorLayout的话，可以使用滑动手势将显示的Snackbar删除。

2. 如果Snackbar显示的时候挡住FAB，解决的办法是用CoordinatorLayout把FAB包起来。

  *错误*

  ![wrong](http://wiki.jikexueyuan.com/project/material-design/images/components-toasts-usage-spec_toast_dont_26_large_mdpi.png)

  *正确*

  ![right](http://wiki.jikexueyuan.com/project/material-design/images/components-toasts-usage-spec_toast_do_26_large_mdpi.png)



3. make()方法的第一个参数的view，不能是有一个ScrollView。因为SnackBar的实现逻辑是往这个View去addView，而ScrollView是只能有一个Child的，否则会Exception。

4. Toast和SnackBar的区别是，前者是悬浮在所有布局之上的包括键盘，而SnackBar是在View上直接addView的。所以SnackBar.show()的时候,要注意先把Keyboard.hide()了，否则，键盘就会遮住SnackBar。

5. 在Android2.3上，SnackBar即使用了CoordinatorLayout也是不支持滑动功能。

6. 在Android2.3上，SnackBar的深色背景颜色和字体颜色相近，可以用SpannableString换一下String的颜色在传给make()方法。
