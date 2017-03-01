# Fragment

Fragment必须始终嵌入Activity中，其生命周期直接受宿主Activity影响。例如：
* 当Activity暂停时，其中所有的Fragment也会暂停
* 当Activity被销毁时，所有Fragment也会被销毁

不过，当Activity正在运行（处于`Resumed`阶段）时，可以独立操纵每个Fragment，比如添加或者移除它们。并且可以把Fragment添加到Activity的**返回栈**中。

## 生命周期

<img src="../Resources/fragment_lifecycle.png"/>

Fragment的生命周期与Activity的生命周期很相似，也以三种状态存在：

* Resumed

  Fragment在运行的Activity中可见

* Paused

  另一个Activity位于前台并具有焦点，但此Fragment所在的Activity仍然可见（前台Activity部分透明，或未覆盖整个屏幕）。

* Stopped

  Fragment不可见。宿主Activity已经停止，或Fragment已经从Activity移除，但已添加到返回栈。停止Fragment仍处于活动状态（系统会保留所有状态和成员信息）。不过，它对用户不再可见，如果Activity被终止，它也会被终止。

同样，Fragment也存在`onSaveInstanceState`保存状态，而且可以再`onCreate()`、`onCreateView()`、`onActivityCreated()`中恢复状态。

Activity生命周期和Fragment生命周期之间最显著的差异在于它们在其各自返回栈的存储方式。默认情况下，Activity停止时会被放入由系统管理的Activity返回栈。不过，只有在移除Fragment的事务执行期间通过`addToBackStack()`显式请求保存实例时，系统才会将Fragment放入宿主Activity管理的返回栈。

## 与Activity生命周期协调一致

<img src="../Resources/activity_fragment_lifecycle.png"/>

一些额外的回调方法：

* `onAttach()`

  此Fragment已与Activity关联时调用（Activity以Context的形式传递到此方法内）

* `onActivityCreated()`

  在Activity的`onCreate()`方法已**返回**时调用

* `onDetach()`

  在取消Fragmeng与Activity的关联时调用

只有Activity处于`Resumed`状态时，Fragment的生命周期才能**独立**发生改变。

不过，一旦Activity离开`Resumed`状态，Fragment就会在Activity的推动下再次经历其生命周期。
