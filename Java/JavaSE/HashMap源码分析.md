# HashMap 源码分析

HashMap 的底层是基于数组和链表实现的，它通过计算散列码来决定存储的位置。HashMap 主要是通过 key 的 hashCode 来计算 hash 值的，只要 hashCode 相同，那么计算出的 hash 值就相同。HashMap 的层是通过冲突列表法解决 Hash 冲突的。

Object 的 `hashCode()` 是一个 native 方法。

HashMap 的 `hash()` 方法就是用来计算 key 的 hash 值。`return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);` 高 16 位与其自身相与。

...
