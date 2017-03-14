# （Android 接口定义语言）AIDL

AIDL 可以定义客户端和服务端使用进程间通信（IPC）进行相互通信时都认可的编程接口。

AIDL 接口的实现必须是线程安全的。

## 定义 AIDL 接口

AIDL 支持下列数据类型：

* Java 中所有的原语类型（如 int、 long、char、boolean 等等）

* String

* CharSequence

* List 只支持 HashMap，里面的每个元素必须是 AIDL 支持的元素。

* Map 只支持 Hashmap，里面的每个元素都必须是 AIDL 支持的元素。

* Parcelable 所有实现了 Parcelable 接口的对象

* AIDL 所有的 AIDL 接口本身也可以在 AIDL 文件中使用

自定义的 Parcelable 对象和 AIDL 对象必须要显式 import 进来，即使是和当前的 AIDL 位于同一个包内。

如果 AIDL 文件中用到了自定义的 Parcelable 对象，那么必须新建一个和它同名的 AIDL 文件，并在其中声明它为 Parcelable 类型。

AIDL 中除了基本数据类型，其他类型的参数必须标上方向：in、out 或 inout，in 表示输入型参数，out 表示输出型参数，inout 表示输入输出型参数。

AIDL 接口中只支持方法，不支持声明静态变量。


## AIDL 接口的分析

* DESCRIPTOR，Binder 的唯一标识、一般当前 Binder 的类名表示。

* asInterface(android.os.IBinder obj)，用于将服务端的 Binder 对象转换成客户端所需的 AIDL 接口类型的对象。这种转换过程是区分进程的，如果客户端和服务位于同一进程的，那么此方法返回的就是服务端的 Stub，否则返回的使系统封装后的 Stub.proxy 对象。

* asBinder，此方法用于返回当前 Binder 对象。

* onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags)，这个方法运行在服务端中的 Binder 线程池中，当客户端发起跨进程请求时，远程请求会通过系统底层封装后交由此方法处理。服务端通过 code 可以确定客户端所请求的目标方法是什么，接着从 data 中取出目标方法所需的参数（如果目标方法有参数的话），然后执行目标方法。当目标方法执行完毕的时候，就向 reply 中写入返回值（如果有的话）。如果此方法返回 false，那么客户端的请求就会失败。
