# Android Note 02 - Android生命周期

* Android系统启动的时候，第一个启动起来的进程就是 **zygote** 进程，然后由zygote启动 **SystemServer** ，再后就是启动 ActivityManagerService、WindowManagerService等系统核心服务，这些服务承载着整个Android系统与客户端程序交互的重担。

* zygote 除了启动系统服务与进程之外，普通的用户进程也由zygote进程fork而来，当一个应用进程启动起来后，就会加载用户在AndroidManifest.xml中配置的默认加载的Activity，此时加载的入口是ActivityThread.main(String[] args)方法，是整个应用程序的入口。

* setContentView的基本流程：
  1. 构建mDecor对象。mDecor就是整个窗口的顶层视图，它主要包括了Title和Content View两个区域，Title区域是我们的标题栏，Content View区域就是显示xml布局内容中的区域。

  2. 设置一些关于窗口的属性，初始化标题栏区域和内容显示区域。

    1. 获取用户设置的一些属性和Flag。
    2. 根据一些属性选择不同的顶层视图布局，如FEATURE_NO_TITLE则选择没有title的布局文件。
    3. 加载顶层布局文件，转换为View，将其添加到mDecor中。
    4. 获取内容容器Content Parent，即用于显示内容的区域。
    5. 设置一些背景图和title等。


* 当Activity启动时，通过onCreate函数让用户设置自己的界面，系统将这个布局界面添加到一个内置的布局界面的content区域中，此时，DecorView就建立起来了。然后调用onStart方法，并且在调用onResume函数之前将DecorView添加到WindowManager中，并且设置Activity为可见，然后通知ActivityManagerService，该Activity已经变为resume状态，使得系统能够渲染Activity的视图，至此，Activity的视图就会显示在手机上了。
