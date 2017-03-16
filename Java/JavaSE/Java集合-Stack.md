# Java集合 - Stack

`Stack`继承于`Vector`，`Vector`则是实现了`List`接口，所以`Stack`本质上也是一个`List`。

## Vector

`Vector`的存储方式也和`ArrayList`一样，使用数组进行存储。

```java
protected Object[] elementData;   //存储数据的数组
```

`Vector`中大部分对数据操作的方法都有`synchronized`修饰符进行修饰，所以`Vector`是一个线程安全的容器。

## Stack

`Stack`则很简单，除了父类的一些方法之外，它还提供了一些它作为栈的一些操作，例如：`push(E):E`、`pop():E`、`peek():E`等等。

而且这些方法都是调用父类方法进行实现的，所以，这些方法都是线程安全的。

PS: `peek():E`的作用是：Looks at the object at the top of this stack without removing it from the stack.
