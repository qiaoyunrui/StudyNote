# IPC 的几种方式

## 使用 Bundle

四大组件中的三大组件（Activity、Service、Reciver）都是支持在 Intent 中传递 Bundle 数据的，由于 Bundle 实现了 Parcelable 接口，所以它可以方便地在不同的进程间传输。当我们在一个进程中启动了另一个进程的 Activity、Servic 和 Receiver，我们就可以在 Bundle 中附加我们需要传输给远程进程的信息并通过 Intent 发送出去。

一种特殊的使用场景：

A 进程在进行一个计算，计算完成后要启动 B 进程的一个组件并把计算结果传递给 B 进程，但是这个计算结果不支持放入 Bundle 中，因此无法通过 Intent 来传输。可以考虑如下方式进行解决：通过 Intent 启动进程 B 的一个 Service 组件，让 Service 在后台进行计算，计算完毕后在启动进程中真正要启动的目标组件，由于 Service 也运行在 B 进程中，所以目标组件就可以直接获取计算结果，这样就解决了跨进程的问题。

## 使用文件共享

两个进程通过读/写同一个文件来交换数据。

## 使用 Messenger

使用 Messenger 可以在 不同进程中传递 Message 对象，在 Message 中放入我们需要传递的数据，就可以轻松地实现数据地进程间通信了。Messenger 是以一种轻量级的 IPC 方案，它的底层是 AIDL。

一次处理一个请求，因此在服务端不用考虑线程同步的问题。

实现 Messenger 的步骤：

1. 服务端进程

在服务端创建一个 Service 来处理客户端的连接请求，同时创建一个 Handler 并通过它来创建一个 Messenger 对象，然后在 Service 的 `onBind()` 中返回这个 Messenger 对象底层的 Binder 即可。

2. 客户端进程

客户端进程中，首先要绑定服务端的 Service，绑定成功后用服务端返回的 IBinder 对象创建一个 Messenger，通过这个 Messenger 就可以向服务端发送信息了，发消息类型为 Message 对象。如果需要服务端能够回应客户端，就和服务端一样，我们还需要创建一个 Handler 并创建一个新的 Messenger，并把这个 Messenger 对象通过 Message 的 replyTo 参数传递给服务端，服务端通过这个 replyTo 参数就可以回应客户端。

![pic](https://www.processon.com/chart_image/58c60e51e4b024ae98f90354.png)

### 示例代码

*服务端*
```Java
package com.juhezi.aidltest;

public class MessengerService extends Service {
    private static final String TAG = "MessengerService";

    private static class MessengerHandler extends Handler {
        @Override
        public void handleMessage(Message msg) {
            switch (msg.what) {
                case 0x123:
                    Log.i(TAG, "handleMessage: receive from Client: " + msg.getData().getString("msg"));
                    Messenger client = msg.replyTo;
                    Message replyMessage = Message.obtain(null, 0x124);
                    Bundle bundle = new Bundle();
                    bundle.putString("reply", "Hello,SkyLoong");
                    replyMessage.setData(bundle);
                    try {
                        client.send(replyMessage);
                    } catch (RemoteException e) {
                        e.printStackTrace();
                    }
                    break;
                default:
                    super.handleMessage(msg);
            }
        }
    }

    private Messenger mMessenger = new Messenger(new MessengerHandler());

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        return mMessenger.getBinder();
    }
}
```

*客户端*
```java
package com.juhezi.aidltest;

public class MainActivity extends AppCompatActivity {

    private static final String TAG = "MainActivity";

    private Messenger mService;

    private static class MessengerHandler extends Handler {
        @Override
        public void handleMessage(Message msg) {
            switch (msg.what) {
                case 0x124:
                    Log.i(TAG, "handleMessage: receive msg from Service: " + msg.getData().getString("reply"));
                    break;
                default:
                    super.handleMessage(msg);
            }
        }
    }

    private Messenger mGetReplyMessenger = new Messenger(new MessengerHandler());


    private ServiceConnection mConnection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            mService = new Messenger(service);
            Message msg = Message.obtain(null, 0x123);
            Bundle data = new Bundle();
            data.putString("msg", "Hello,Juhezi");
            msg.setData(data);
            msg.replyTo = mGetReplyMessenger;
            try {
                mService.send(msg);
            } catch (RemoteException e) {
                e.printStackTrace();
            }
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {

        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Intent intent = new Intent(this, MessengerService.class);
        bindService(intent, mConnection, Context.BIND_AUTO_CREATE);
    }

    @Override
    protected void onDestroy() {
        unbindService(mConnection);
        super.onDestroy();
    }

    public static void actionStart(Context context, String data1, String data2) {
        if (context == null) return;
        Intent intent = new Intent(context, MainActivity.class);
        intent.putExtra("param1", data1);
        intent.putExtra("param2", data2);
        context.startActivity(intent);
    }

}
```

## 使用 AIDL

Messenger 是以串行的方式处理客户端发来的消息，如果大量的消息同时发送到服务端，服务端仍然只能一个个处理，如果有大量的并发请求，那么用 Messenger 就不合适了。同时 Messenger 的作用主要是为了传递消息，跨进程调用服务端的方法 Messenger 就办不到了。但是我们可以使用 AIDL 来实现跨进程的方法调用。

1. 服务端

服务端首先要创建一个 Service 用来监听客户端的连接请求，然后创建一个 AIDL 文件，将暴露给客户端的接口在这个 AIDL 文件中声明，最后在 Service 中实现这个 AIDL 接口即可。

2. 客户端

客户端首先要绑定服务端的 Service，绑定成功后，将服务端返回的 Binder 对象转成 AIDL　接口所属的类型，接着就可以调用 AIDL 中的方法了。

RemoteCallbackList 是系统专门提供的用于删除跨进程 listener 的接口。它的内部有一个 Map 结构专门用来保存所有的 AIDL 回调，这个 Map 的 key 是 IBinder 类型，value 是 Callback 类型。RemoteCallbackList 内部实现了线程同步的功能。当客户端进程终止的时候，RemoteCallbackList 能够自动移除客户端所注册的 listener。

遍历 RemoteCallbackList 时，要使用 beginBroadcast 和 finishBroadcast。

虽然多次跨进程传输客户端的同一个对象会在服务端生成不同的对象，但是这些新生成的对象有一个共同点，那就是它们底层的 Binder 对象是同一个。

客户端调用远程服务的方法，被调用的方法运行在服务端的 Binder 线程池中，同时 **客户端线程会被挂起**。如果明确知道某个远程方法是耗时的，那么就要避免在客户端 UI 线程中去访问远程方法。由于客户端的 onServiceConnected 和 onServiceDisconnected 方法都运行在 UI 线程中，所以也不可以在它们里面直接调用服务端的耗时方法。由于服务端的方法本身就运行在服务端的 Binder 线程池中，所以服务端方法本身就可以执行大量耗时任务，这个时候不要在服务端方法中开线程去进行异步任务。


### 为 Binder 设置死亡代理

```java
service.linkToDeath(new IBinder.DeathRecipient() {
    @Override
    public void binderDied() {
      //在客户端的 Binder 线程池中调用
    }
}, 0);
```

## 使用 ContentProvider

ContentProvider 的底层实现也是 Binder。

## 使用 Socket

..
