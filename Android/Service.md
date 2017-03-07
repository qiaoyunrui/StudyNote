# Service

Service 是一个可以在后台长时间运行而不提供用户界面的应用组件。Service 由其他组件启动，即使用户切换到其他应用，Service 仍会在后台继续运行。

此外，组件可以绑定到 Service，从而与之进行交互，甚至是执行进程间通信（IPC）。

Service 基本上可以分为两种形式：

* 启动

  当应用组件通过`startService()`启动服务的时候，Service 即处于“启动状态”。一旦启动，Service 即可在后台无限制运行，即使启动 Service 的组件已被销毁也不受影响。已启动的 Service 通常是执行单一操作，而且不将结果返回调用方。例如，通过网络下载或上传文件。操作完成后，服务会自行停止运行。

* 绑定

  当应用组件通过调用`bindService()`绑定到 Service 时，Service 即处于“绑定”状态。绑定 Service 提供了一个客户端-服务器接口，允许组件与 Service 进行交互、发送请求、获取结果。甚至是利用 IPC 执行这些操作。仅当与另一个应用组件绑定时，绑定 Service 才会运行。多个组件可以同时绑定到该 Service，但全部取消绑定后，该 Service 就会被销毁。

**注意**：Service 在其托管的进程的**主线程**中运行。

## 生命周期

![生命周期](../Resources/service_lifecycle.png)

由图可以看出，Service共有两种周期，这是因为 Service 有两种类型的缘故。

左面的生命周期就是“启动”模式的 Service，而右图是“绑定”模式的 Service。

* `onStartCommand()` 当另一个组件通过调用`startService()`请求启动 Service 时，系统会调用此方法。一旦执行此方法，Service 即会启动并可在后台无期限运行。如果实现此方法，则在服务工作完成后，需要调用`stopSelf()`或`stopService`来停止服务。

* `onBind()`  如果另一个组件通过调用`bindService()`与服务进行绑定时，系统将调用此方法。在此方法的实现中，要通过返回一个`IBinder`提供一个接口，供客户端来与服务端进行通信。
