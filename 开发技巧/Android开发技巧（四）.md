# Android开发技巧（四）

## 严格模式

###开启严格模式

```java
if (DebugUtil.DEBUG && Build.VERSION.SDK_INT >= Build.VERSION_CODES.GINGERBREAD) {
    StrictMode.setThreadPolicy(new StrictMode.ThreadPolicy.Builder().detectAll().penaltyLog().build());
    StrictMode.setVmPolicy(new StrictMode.VmPolicy.Builder().detectAll().penaltyLog().build());
}
```

### 具体能检测什么

严格模式主要检测两大问题，一个是线程策略，即TreadPolicy，另一个是VM策略，即VmPolicy。

**ThreadPolicy**

线程策略检测的内容有

* 自定义的耗时调用 使用detectCustomSlowCalls()开启
* 磁盘读取操作 使用detectDiskReads()开启
* 磁盘写入操作 使用detectDiskWrites()开启
* 网络操作 使用detectNetwork()开启


**VmPolicy**

虚拟机策略检测的内容有

* Activity泄露 使用detectActivityLeaks()开启
* 未关闭的Closable对象泄露 使用detectLeakedClosableObjects()开启
* 泄露的Sqlite对象 使用detectLeakedSqlLiteObjects()开启
* 检测实例数量 使用setClassInstanceLimit()开启
