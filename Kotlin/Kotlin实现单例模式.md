# Kotlin实现单例模式

## 静态内部类 · 单例模式

```JavaScript
class B private constructor() {
    companion object {
        fun getInstance() = Holder.sIntance
    }

    private object Holder {
        val sIntance = B()
    }
}
```

## 同步懒加载 · 单例模式

```JavaScript
class A {
    companion object {
        val instance by lazy(LazyThreadSafetyMode.SYNCHRONIZED) {
            A()
        }
    }
}
```
