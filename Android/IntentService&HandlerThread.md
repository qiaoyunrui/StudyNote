---
title: IntentService 和 HandlerThread 的原理
date: 2017-05-19 09:57:47
tags: Android
---
相信大家在 Android 开发中也用到过 IntentService 和 HandlerThread 这两个类，那么我们今天就来分析一下这两个类。

## 什么是 IntentService?

IntentService 是 Service 的一个子类，它的内部有一个 Handler 和 HandlerThread。所以 IntentService 与 Service 最大的不同就是 IntentService 在后台开启了一个子线程，而 Service 并没有，它还是在 UI 线程里。

IntentService 通过 Handler 和 HandlerThread 来开启一个线程，那么我们先来看一看 HandlerThread 的源码。

## HandlerThread

只贴 HandlerThread 的部分代码：

```java
package android.os;

public class HandlerThread extends Thread {
    int mPriority;
    int mTid = -1;
    Looper mLooper;

    public HandlerThread(String name) {
        super(name);
        mPriority = Process.THREAD_PRIORITY_DEFAULT;
    }

    public HandlerThread(String name, int priority) {
        super(name);
        mPriority = priority;
    }

    /**
     * Call back method that can be explicitly overridden if needed to execute some
     * setup before Looper loops.
     */
    protected void onLooperPrepared() {
    }

    @Override
    public void run() {
        mTid = Process.myTid();
        Looper.prepare(); //在本线程中创建一个 Looper。
        synchronized (this) {
            mLooper = Looper.myLooper();
            notifyAll();
        }
        Process.setThreadPriority(mPriority);	//设置线程优先级
        onLooperPrepared();
        Looper.loop();	//looper 循环，开始从 MessageQueue 获取 messages
        mTid = -1;
    }
}

```

从代码里可以看到，HandlerThread 继承于 Thread ，并重写了 run() 方法。
在 run() 方法里，通过 Looper.prepare() 创建当前线程的 Looper。随后设置当前线程的优先级，并调用 Looper.loop() 使当前线程的 Looper 开始循环，开始从 Looper 自带的 MessageQueue 中获取消息。所以，HandlerThread 就是一个自带消息队列的 Thread。

使用 HandlerThread 的时候要配合 Handler 一起使用，使用例子如下：

```java
HandlerThread handlerThread = new HandlerThread("Juhezi");
handlerThread.start();	//开启线程，同时也开启了消息循环
Looper looper = handlerThread.getLooper();	//获取 HandlerThread 中的 Looper
Handler handler = new Handler(looper);		//通过 Looper 初始化 Handler
```

这时，通过 handler 发送的消息，都会存储在 handlerThread 中的消息队列里。

如果想让 handlerThread 退出，只需要执行 handlerThread 的 quit()\quitSafely() 方法，这里其实是调用了 looper 的 quit()\quitSafely() 方法。

## IntentService

接下来就开始正式分析 IntentService。

IntentService 中有一个内部类 ServiceHandler，它继承了 Handler，它的构造方法需要一个 Looper。IntentService 就是这个 ServiceHandler 的实例来进行发送消息，处理消息的。
ServiceHandler 重载的 handleMessage() 中就是对消息的处理方式，从代码可见，它首先执行一个 onHandleIntent() 方法，然后执行 stopSelf 结束 Service（自己）。那个 onHandlerMessage() 是一个抽象方法。在这里可以得出一个结论，

```java
private final class ServiceHandler extends Handler {
    public ServiceHandler(Looper looper) {
        super(looper);
    }

    @Override
    public void handleMessage(Message msg) {
        onHandleIntent((Intent)msg.obj);	//回调
        stopSelf(msg.arg1);	//结束 Service
    }
}
```

然后来看 onCreate() 方法，仔细一看，不就是和我们上面讲过的 HandlerThread 一个套路嘛：创建一个 HandlerThread，调用其 start 方法，然后获取根据 HandlerThread 的 Looper 创建一个 Handler，不过这里是它的子类：ServiceHandler。

```java
@Override
public void onCreate() {

    super.onCreate();
    HandlerThread thread = new HandlerThread("IntentService[" + mName + "]");
    thread.start();

    mServiceLooper = thread.getLooper();
    mServiceHandler = new ServiceHandler(mServiceLooper);
}
```
接着来看 onStart() 方法，很明显，就是把传入的 Intent 和 startId 包装成 message，通过 mServiceHandler 发送出去，这个时候，你就可以在重载的 onHandlerMessage 中处理这个信息。
注意：这个重载的 onHandlerMessage 是在工作线程中被调用的，所以你可以在这里执行一些耗时任务。

```java
@Override
public void onStart(@Nullable Intent intent, int startId) {
    Message msg = mServiceHandler.obtainMessage();
    msg.arg1 = startId;
    msg.obj = intent;
    mServiceHandler.sendMessage(msg);
}
```

通过源码，我们还知道，不建议通过 bindService() 的 方式启动 IntentService，建议使用 startService() 的方式。


最后，我们看一下这个 IntentService 的具体使用方法：

```java
public class TestService extends IntentService {

    private static final String ACTION_FOO = "com.juhezi.test.action.FOO";
    private static final String ACTION_BAZ = "com.juhezi.test.action.BAZ";

    private static final String EXTRA_PARAM1 = "com.juhezi.test.extra.PARAM1";
    private static final String EXTRA_PARAM2 = "com.juhezi.test.extra.PARAM2";

    public static final String TAG = "TestService";

    public TestService() {
        super("TestService");
    }

    public static void startActionFoo(Context context, String param1, String param2) {
        Intent intent = new Intent(context, TestService.class);
        intent.setAction(ACTION_FOO);
        intent.putExtra(EXTRA_PARAM1, param1);
        intent.putExtra(EXTRA_PARAM2, param2);
        context.startService(intent);
    }

    public static void startActionBaz(Context context, String param1, String param2) {
        Intent intent = new Intent(context, TestService.class);
        intent.setAction(ACTION_BAZ);
        intent.putExtra(EXTRA_PARAM1, param1);
        intent.putExtra(EXTRA_PARAM2, param2);
        context.startService(intent);
    }

    @Override
    protected void onHandleIntent(Intent intent) {
        if (intent != null) {
            final String action = intent.getAction();
            if (ACTION_FOO.equals(action)) {
                final String param1 = intent.getStringExtra(EXTRA_PARAM1);
                final String param2 = intent.getStringExtra(EXTRA_PARAM2);
                handleActionFoo(param1, param2);
            } else if (ACTION_BAZ.equals(action)) {
                final String param1 = intent.getStringExtra(EXTRA_PARAM1);
                final String param2 = intent.getStringExtra(EXTRA_PARAM2);
                handleActionBaz(param1, param2);
            }
        }
    }

    /**
     * Handle action Foo in the provided background thread with the provided
     * parameters.
     */
    private void handleActionFoo(String param1, String param2) {
        Log.i(TAG, "handleActionFoo: " + param1 + " " + param2);
    }

    /**
     * Handle action Baz in the provided background thread with the provided
     * parameters.
     */
    private void handleActionBaz(String param1, String param2) {
        Log.i(TAG, "handleActionBaz: " + param1 + " " + param2);
    }
}
```

感谢阅读。
