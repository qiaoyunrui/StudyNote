# 获取View宽高的方式

众所周知，在 Activity 的 onCreate()、onResume()、onStart() 方法中是无法获取 View 的宽和高的，因为 View 的 measure 过程和 Activity 的生命周期方法不是同步执行的，因此无法保证 Activity 执行了 onCreate、onStart、onResume 某个 View 已经测量完毕了。

## Activity/View#onWindowFocusChanged

需要注意的是：onWindowFocusChanged 会被调用多次，当 Activity 的窗口得到焦点和失去焦点均会被调用一次。具体来说，当 Activity 继续执行或者暂停执行时，onWindowFocusChanged 均会被调用。

## View.post(runnable)

通过 post 可以将一个 runnable 投递到消息队列的尾部，然后等待 Looper 调用此 runnable 的时候，View 也已经被初始化好了。

```JavaScript
contentView.post {
    var height = contentView.height
    var width = contentView.width
}
```

## ViewTreeObserver

...
