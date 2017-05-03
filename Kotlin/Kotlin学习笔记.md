# Kotlin 学习笔记 0x00

## Compile-Time Constants (编译时常量)

需要满足以下三个条件：

* Top-level or member of an `object` (必须是顶级的，或者是一个 object 的一个成员变量)
* Initialized with a value of type String or a primitive type (仅支持基本数据类型和字符串)
* No custom getter (没有自定义的 getter)

编译时常量可用于注解中。

## `lateinit` 修饰符

用于类内部（不是在主构造方法里）的 var 变量，并且该变量没有自定义的 getter 和 setter 方法，该属性的类型必须为非空，并且不可以是一个原始数据类型。

在 `lateinit` 变量被初始化之前访问时，会抛出异常。

## `infix` 修饰符

修饰方法，中缀表达式

## Local Function (局部方法)

局部方法可以使用外部方法的参数

## `tailrec` 修饰符

修饰方法，表示尾递归

## 其他

* 重载的方法不允许有默认参数
