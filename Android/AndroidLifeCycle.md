  # Android 生命周期

![Activity Life Cycle](http://ww4.sinaimg.cn/mw690/005TG3l2jw1faxhqx4h4kj30ie0873zi.jpg)

所示为Activity生命周期图，以阶梯金字塔表示。

当系统创建新 Activity 实例时，每个回调方法会将 Activity 状态向顶端移动一阶。 金字塔的顶端是 Activity 在前台运行并且用户可以与其交互的时间点。

当用户开始离开 Activity 时，系统会调用其他方法在金字塔中将 Activity 状态下移，从而销毁 Activity。 在有些情况下，Activity 将只在金字塔中部分下移并等待（比如，当用户切换到其他应用时），Activity 可从该点开始移回顶端（如果用户返回到该 Activity），并在用户停止的位置继续。


## 三种状态

Activity 只能在一下三种状态存在很长时间：

1. **Resumed**

  Activity 处于前台，且用户可以与之交互。

2. **Paused**

  Activity 被在前台中处于半透明状态或者未完全覆盖整个屏幕的另一个 Activity 一部分阻挡。暂停的 Activity 不会接受用户输入并且执行任何代码。

3. **Stopped**

  Activity 被完全隐藏并且对用户不可见。它被视为处于后台。停止时，Activity 实例及诸如成员变量等所有状态信息将保留，但无法执行任何代码。

其他状态（`Create`和`Start`）是瞬间状态，系统会通过调用下一个生命周期从这个状态快速切换到下一个状态。也就是说，在系统调用`onCreate()`之后，会快速调用`onStart()`，紧接着快速调用`onResume()`。

## `onDestory()` - 销毁 Activity

大多数应用不需要实现这个方法，因为局部引用类与Activity一同销毁，并且Activity应该在`onPause()`和`onStop()`期间执行大多数清理操作。但是如果Activity包含在`onCreate()`期间创建的后台线程或其他若未正常关闭可能导致内存泄漏的**长期**运行资源，应该在`onDestroy()`期间终止它们。

**注意**：在所有情况下，系统在调用`onPause()`和`onStop()`之后都会调用`onDestroy()`，只有一个例外：当在`onCreate()`方法中调用`finish()`时，系统会立即调用`onDestroy()`，而不调用其他生命周期方法。

## 暂停Activity

在应用正常使用过程中，应用有时会失去焦点，导致Activity暂停。例如：在多窗口模式下（Android N提供）运行，仅有一个应用随时具备焦点，系统将暂停其他应用。

但是，一旦Activity被完全阻挡并且不可见，Activity就会停止。

Activity进入暂停状态，系统会执行`onPause()`方法。

在`onPause()`方法中应该执行的操作有：

1. 检查Activity是否可见。如果不可见，应该停止动画，或其他正在进行的消耗CPU的操作。

2. 提交未保存的更改（例如邮件的草稿）。

3. 释放系统资源，比如广播接收器、Camera等等。

避免在`onPause()`中进行CPU密集型操作，比如向数据库写入信息，因为这会拖慢向下一个状态过渡的过程（应该在`onStop（）`中进行高负载相关操作）。

**注意**：当Activity暂停时，Activity实例将驻留在**内存**中，并且在Activity继续时再次被调用。

## 继续 Activity

当Activity从“暂停”状态继续时，会执行`onResume()`方法。

每当Activity进入前台的时候，系统便会调用`onResume()`方法，包括初次创建。

应该实现`onResume()`以初始化在`onPause()`期间释放的组件，并执行每当 Activity 进入“继续”状态时必须进行的任何其他初始化操作（比如开始动画和初始化只在 Activity 具有用户焦点时使用的组件）。

例如在onResume()中初始化相机。

## 停止和重启Activity

Activity停止和重启的场景主要是以下几种：

1. 用户打开“最近应用”窗口并从您的应用切换到另一个应用。您的应用中当前位于前台的 Activity 将停止。 如果用户从主屏幕启动器图标或“最近应用”窗口返回到您的应用，将会重启 Activity。

2. 用户在本应用中执行开始新Activity的操作。当第二个 Activity创建好后，当前Activity便停止。如果用户之后按了返回按钮，第一个 Activity 会重新开始。

3. 用户在其手机上使用本应用的同时接听来电。

**注意**：因为系统在 Activity 停止时会将 Activity 实例保留在系统内存中，所以根本无需实现 onStop() 和 onRestart() 或甚至onStart() 方法。对于大多数相对简单的 Activity 而言，Activity 将停止并重新启动，并且可能只需使用 onPause() 暂停正在进行的操作，并从系统资源断开连接。

当Activity收到`onStop()`方法的调用时，它不再可见，并且释放几乎所有用户不使用时不需要的资源。一旦Activity停止，如过需要恢复系统内存，系统可能会销毁该实例。在极端情况下，系统可能会仅终止应用进程，而不回调用Activity的`onDestroy()`方法，因此要在`onStop()`方法中释放可能泄漏内存的资源。

**注意**：即使系统在 Activity 停止时销毁了 Activity，它仍会保留 Bundle（键值对的二进制大对象）中的 View 对象（比如 EditText 中的文本），并在用户导航回 Activity 的相同实例时恢复它们。

当Activity从后台状态返回前台时，会执行`onRestart()`方法。每次Activity变为可见时，系统都会调用`onStart()`方法（无论是正重新开始还是初次创建）。但是，只会在Activity从停止状态继续时调用`onRestart()`方法。
