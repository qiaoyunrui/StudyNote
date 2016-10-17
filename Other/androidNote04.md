# Android Note 04 - Binder

* Binder机制的本质是一个中介者模式，Binder机制是Android中非常重要的一个机制，其用来绑定不同的系统服务并进行跨进程通信。在Binder机制中，有三个非常重要的组件ServiceManager、Binder Driver和Bp Binder，其中Bp Binder是Binder中的一个**代理**角色，提供IBinder接口给各个客户端服务使用。

* 当手机启动后，ServiceManager会先向Binder Driver进行注册，它在Binder Driver中是最先被注册的，其注册ID为 0，当其他的服务想要注册到Binder Derive时，会先通过这个0号ID获取到ServiceManager所对应的IBinder接口，该接口实质上的实现逻辑是由Bp Binder 实现的，获取对应的接口后就回调其中的```transact()```方法，此后就会在Binder Driver中新注册一个ID 1对应这个服务。如果有客户端想使用这个服务，那么，它会先像Binder Driver 一样获取到ID 为 0 的接口，也就是ServiceManager所对应的接口，并调用transact方法要求连接到刚才的服务，这时候Binder Driver就会将ID为1的服务回传给客户端并将相关信息反馈给 ServiceManager 完成连接。这里ServiceManager 和 Binder Driver 就相当于一个中介者，协调各个服务端与客户端。

*
