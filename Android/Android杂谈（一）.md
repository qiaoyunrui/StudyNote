# Android杂谈（一）

## 隐式Intent

`android.intent.category.DEFAULT`是一种默认的`category`，在调用`startActivity()`方法的时候会自动将这个`category`添加到`Intent`中。

每个`Intent`中只能指定一个`action`，但却能指定多个`category`。

`<intent-filter>`标签中可以配置`<data>`标签，用于更精确地指定当前`Activity`能够响应什么类型的数据。`<data>`标签中主要可以配置以下内容：

* `android:scheme` - 指定数据的协议部分
* `android:host` - 指定数据的主机名部分
* `android:port` - 指定数据的端口部分
* `android:mimeType` - 指定可以处理的数据类型，允许使用通配符的方式进行指定

只有`<data>`标签中指定的内容和Intent中携带的`Data`完全一致时，当前`Activity`才能响应该`Intent`。

## `onSaveInstanceState(Bundle):void` & `onRestoreInstanceState(Bundle):void`

`onSaveInstanceState()`的作用是保存实例状态，它的调用时机有：

1. 按`Home`键回到主界面
2. 按多任务键打开多任务视图
3. 打开另一个`Activity`（全屏，并不是对话框样式）
4. 锁屏
5. 屏幕方向切换时

`onRestoreInstanceState()`的作用是恢复实例状态，它的调用时机有：

1. 屏幕方向切换时

**注意：** 这两个方法不一定是成对出现的，`onRestoreInstanceState`
被调用的前提是，该`Activity`确实被销毁了。另外，`onRestoreInstanceState`的`Bundle`参数也会传递到`onCreate()`方法中，可以在`onCreate`中恢复数据。


## `Activity`的启动模式

启动模式共有四种，分别是：`standard`、`singleTop`、`singleTask`、`singleInstance`，可以在`AndroidMenifest.xml`中通过给`<activity>`标签指定`android:launchMode`属性来选择启动模式。

### `standard`

`Activity`默认的启动模式。

在该默认情况下，每启动一个新的`Activity`，它就会在返回栈中入栈，并处于栈顶的位置。系统不会在乎这个`Activity`是否已经在返回栈中存在，每次启动都会重新创建一个新的实例。

### `singleTop`

当`Activity`启动时，如果发现返回栈的栈顶已经是该`Activity`，那么就会直接使用它。如果栈顶不是该`Activity`，那么仍会创建新的实例。

### `singleTask`

每次启动`Activity`的时候，系统首先会在返回栈检查是否存在该活动的实例，如果有则直接使用该实例，并把这个`Activity`上所有的`Activity`全部出栈，如果没有发现，则创建一个新的实例。

### `singleInstance`

指定为`singleInstance`启动模式的`Activity`会启用一个**新**的返回栈来管理这个`Activity`。

## 杀掉当前进程

```java
android.os.Process.killProcess(android.os.Process.myPid());
```
