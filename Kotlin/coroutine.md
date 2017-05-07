# Kotlin Coroutine

    Coroutine 是一个轻量级的线程。

`launch(CommonPool) { ... }` 可以类比为 `thread { ... }`，而 `delay(...)` 则可以类比为 `Thread.sleep(...)`。

`runBlocking { ... }` 用于开启一个主 Coroutine。`runBlocking` 之外的代码会阻塞，直到 `runBlocking` 中的 Coroutine 结束。

Coroutine 类似于守护线程。
