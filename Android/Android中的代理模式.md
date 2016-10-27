# Android中的代理模式

本文讨论的是ActivityManagerNative与ActivityManagerProxy所构成的代理模式。

单单从名字上来说的话，ActivityManagerProxy必然是一个代理类，而相应的ActivityManagerNative就是那个被代理的类。

口说无凭，让我们来真正探讨一下它们之间的关系。

## ActivityManagerNative&ActivityManagerProxy

ActivityManagerProxy与ActivityManagerNative是处于一个文件下面的，即 *ActivityManagerNative.java* 文件内。

ActivityManagerProxy实现了IActivityManager接口，IActivityManager接口继承了IInterface接口。

*IInterface.java*
```java

package android.os;

/**
 * Base class for Binder interfaces.  When defining a new interface,
 * you must derive it from IInterface.
 */
public interface IInterface
{
    /**
     * Retrieve the Binder object associated with this interface.
     * You must use this instead of a plain cast, so that proxy objects
     * can return the correct result.
     */
    public IBinder asBinder();
}
```

由注释可知：asBinder()方法的作用就是 *检索与此接口相关联的Binder对象*。


IActivityManager中的方法都是一些和四大组件相关的方法，例如：

* startActivity()

* finishActivity()

* registerReceiver()

* unregisterReceiver()

* bindService()

* unbindService()

* ...

IActivityManager就相当于代理模式中的抽象主题，那么真正的实现主题是什么？

由下图就可以看出：

<img alt="假装是UML" src="http://ww4.sinaimg.cn/large/005TG3l2jw1f8vd9zdmyyj30hk0en3zz.jpg"/>

从上图可看出，ActivityManagerProxy和ActivityManagerNative都实现了IActivityManager接口，严格来讲，ActivityManagerProxy就是代理部分，而ActivityManagerNative就是真实部分，但是ActivityManagerNative是一个抽象类，并不处理其中的逻辑，大部分具体逻辑是由其子类ActivityManagerService实现的，所以真实部分应该是ActivityManagerService而不是ActivityManagerNative。

ActivityManagerService是 **系统级** 的Service并且运行于 **独立的进程空间中**，可以通过ServiceManager来获取它。而ActivityManagerProxy也运行在自己所处的进程空间中，两者并不相同，因此ActivityManagerProxy与ActivityManagerService的通信是跨进程通信（IPC）。此处所实现的代理实质上为远程代理。

ActivityManagerProxy在实际的逻辑处理中并未过多地被外部类使用，因为在Android中管理与维护Activity相关信息的类是另一个叫做ActivityManager的类。ActivityManager虽说管理者Activity的信息，但本质上大多数逻辑操作都是由ActivityManagerProxy承担的。
