# Kotlin 中使用 DSL 实现建造者模式

想必大家对建造者模式也有一定了解，我们今天不走寻常路，使用 DSL 的方法优雅地实现建造者模式。

## 先上代码

```js
data class User(var id: String = "",
                var username: String = "default",
                var password: String = "000000")


fun buildUser(buildAction: User.() -> Unit) = User().apply(buildAction)

fun modifyUser(user: User, modifyAction: User.() -> Unit) = user.apply(modifyAction)

fun main(args: Array<String>) {
    var user = buildUser {
        id = UUID.randomUUID().toString()
        username = "Juhezi"
        password = "123456"
    }
    println(user)
    user = modifyUser(user) {
        id = "0"
        username = "Kotlin"
        password = "654321"
    }
    println(user)
}
```

### 运行结果
```java
User(id=d762b3d7-fe69-4735-bed4-a99b2e88bd82, username=Juhezi, password=123456)
User(id=0, username=Kotlin, password=654321)
```

## 分析一波

代码和效果都看过了，接下来我们细细分析一下这段代码。

首先，第一行定义了一个 `data class`，在 Kotlin 中 `data class` 的作用是： `Create a POJO with getters, setters, equals(), hashCode(), toString() and copy() in a single line`，也就是默认实现了 `getters` 和 `setters` 方法。

然后就是 `fun buildUser(buildAction: User.() -> Unit) = User().apply(buildAction)` 这个 build 方法，这个方法需要一个 `User.() -> Unit` 类型的参数，这个类似你可能不明白是什么，其实，它指得是一个函数类型，即 User 的一个方法，这个方法会返回 Unit。

而 User().apply(buildAction) 的作用就是，首先创建一个 User() 对象，然后执行传入的 buildAction 方法，然后返回这个 User() 对象。

```js
var user = buildUser {
    id = UUID.randomUUID().toString()
    username = "Juhezi"
    password = "123456"
}
```

可以看到，我们在大括号里直接就可以修改 User 的 id，这里其实是调用了 id 的 setter，前面说过了，`data class` 会自动实现 getters 和 setter 方法。同时那个大括号可以看作是刚刚创建的 User 的方法体，在自己的内部，当然可以直接使用自己的方法咯。

下面的 `modifyUser`也是一样的。

比较一下，使用 DSL 实现建造者模式是不是减少了大量实现代码呢。
