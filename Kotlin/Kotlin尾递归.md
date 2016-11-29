# Kotlin尾递归

## 使用递归的方法求阶乘

```javaScript
class Result(var value: BigInteger = BigInteger.valueOf(1L))

tailrec fun factorial(num: Int, result: Result) {
    if (num == 0) {
        result.value = result.value.times(BigInteger.valueOf(1L))
    } else {
        result.value = result.value.times(BigInteger.valueOf(num.toLong()))
        factorial(num - 1, result)
    }
}

fun main(args: Array<String>) {
    var result = Result()
    factorial(1000000, result)
    println(result.value)
}
```

**PS**：`tailrec`关键字表示使用尾递归的方式执行

如果使用一般的递归的话，计算大数的阶乘时会发生`StackOverFlow`，而使用尾递归则不会发生这种情况。（本质上是把递归转换为迭代了）
