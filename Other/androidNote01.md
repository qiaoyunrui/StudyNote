# Android Note 01 - 状态保存

* Activity的onSaveInstanceState()函数中，主要分为3步：
  * 存储窗口的视图树的状态
  * 存储Fragment的状态
  * 调用Activity的ActivityLifecycleCallbaks的onSaveInstanceState函数进行状态存储


* 在存储窗口的视图树状态的过程中，首先要存储整棵视图树的结构，然后保存当前界面中获取了焦点的View（PS：持有焦点的View必须要设置id，否则重现新进入界面时不会恢复它的焦点状态），之后是存储整个面板的状态以及ActionBar的状态。在存储View状态的过程中，如果该View没有设置ID的话，这个View的状态不会被存储到Bundle中。

* View的状态是通过SparseArray来存储的，这相当于一个map，它的key是View的id、value为View的状态。当在一个视图树中含有两个相同的id时，那么必然出现状态覆盖的情况。

* 当需要存储一个View的状态的时，需要覆盖onSaveInstanceState方法，将要存储的数据放到Parcelable对象中，并且将它返回。

* 存储完Window的视图树状态信息之后，便会执行存储Fragment中的状态信息、回退栈等。存储Fragment状态信息也是调用它的onSaveInstanceState方法，存储Fragment的View视图树状态。

* 最后是调用AndroidLifecycleCallbacks 的onSaveInstanceState方法

* ActivityClientRecord的state字段存储Activity的信息后，调用Activity的onSto()方法。（Activity的onStop方法在ActivityThread的performStopActivity函数中）

* onSaveInstanceState的调用时机：当某个Activity变得容易被系统销毁时，该Activity的onSaveInstanceState函数就会被执行，除非该Activit是被用户主动销毁的，如当用户按下Back键时。具体情况如下：
  * 当用户按下Home键
  * 按多任务键，切换到其他应用
  * 按下电源键（关闭屏幕显示）
  * 从ActivityA中启动一个新的Activity时
  * 屏幕方向切换时
  * 电话打入等情况
