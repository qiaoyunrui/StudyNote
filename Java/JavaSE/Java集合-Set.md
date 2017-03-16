# Java集合 - Set

## Set接口

`Set`中不包含相同元素。其中“相同”是通过`equals()`进行判断的。

## HashSet

`HashSet`内部持有一个`HashMap`对象，对数据进行的操作（`add(E):boolean`、`remove(Object):boolean`、`size():int`）都是对内部的`HashMap`进行操作。所以，`HashSet`本质上相当于一个`HashMap`。

## LinkedHashSet

*继承于`HashSet`*

只是实现了几个构造方法，并且这几个构造方法都是直接使用了父类的构造方法。

`LinkedHashSet`所使用的父类构造方法：
```Java
HashSet(int initialCapacity, float loadFactor, boolean dummy) {
    map = new LinkedHashMap<>(initialCapacity, loadFactor);
}
```

`HashSet`其他的构造方法：
```Java
public HashSet(int initialCapacity) {
    map = new HashMap<>(initialCapacity);
}
```

只有在`HashSet`的这个构造方法中，把`map`实例化`LinkedHashMap`，而其他的构造方法则是把`map`实例化为`HashMap`。所以`LinkedHashSet`的实现是通过`LinkedHashMap`实现的。

**注意：** 无论是`HashSet`还是`LinkedHashSet`，它们本质上是一个值（`Value`）为同一`Object`（即是源码中的`PRESENT`）的`Map`。


## TreeSet

*实现了`NavigableSet`接口*

`NavigableSet`接口提供了一系列导航方法，例如：

* lower(E):E
* floor(E):E
* ceiling(E):E
* higher(E):E
* ...

`TreeSet`内部持有一个`NavigableMap`引用，然后在`TreeSet`的构造方法中将该引用实例化为一个`TreeMap`对象。和`HashSet`类似，对HashSet的相关操作基本上都是通过执行`TreeMap`的相关方法实现的。

## 以上三者的区别

* `HashSet`：哈希表是通过使用称为散列法的机制来存储信息的，元素并没有以某种特定顺序来存放

* `LinkedHashSet`：以元素插入的顺序来维护集合的链接表，允许以插入的顺序在集合中迭代

* `TreeSet`：提供一个使用树结构存储Set接口的实现，对象以升序顺序存储，访问和遍历的时间很快
