# ConcurrentHashMap 的实现原理

**注意：这篇笔记是针对 Java 8 以前的源码进行分析**

ConcurrentHashMap 是线程安全且高效的 HashMap。

## 使用 ConcurrentHashMap 的必要性

在并发编程中使用 HashMap 可能导致程序死循环。而使用线程安全的 HashTable 效率又十分低下。

* 线程不安全的 HashMap

  在多线程环境下，使用 HashMap 进行 put 操作会引起死循环，是因为多线程会导致 HashMap 的 Entry 链表形成环形数据结构，一旦形成环形数据结构，Entry 的 next 节点永远不为空。

* 效率低下的 HashTable

  HashTable 容器使用 synchronized 来保证线程安全，但在线程竞争激烈的情况下 HashTable 的效率非常低下，因为当一个线程访问 HashTable 的同步方法，其他线程也访问  HashTable 的同步方法时，会进入阻塞或者轮询的状态。

* ConcurrentHashMap 的锁分段技术可有效提升并发访问率

  HashTable 容器在竞争激烈的并发环境下表现出效率低下的原因是所有访问 HashTable 的线程都必须竞争同一把锁。而 ConcurrentHashMap 则是把数据分为一段一段地存储，然后给每一段数据配一把锁。

## 结构

ConcurrentHashMap 是由 Segment 数组结构和 HashEntry 数组结构组成。Segment 是一种可重入锁（ReentranLock），在 ConcurrentHashMap 扮演锁的角色；HashEntry 则用于存储键值对数据。

![结构图](https://www.processon.com/chart_image/58dd9f28e4b0fdc02219c94c.png)


## ConcurrentHashMap 的初始化

ConcurrentHashMap 初始化方法是通过 initialCapacity、loadFactor 和 ConcurrencyLevel 等几个参数来初始化 egment 数组、偏移量 segmentShift、段掩码 segmentMask 和每个 segment 里的 HashEntry 数组来实现的。

1. 初始化 segments 数组

  segemnts 数组的长度 ssize 是通过 ConcurrencyLevel 计算得出的。为了能够通过按位与的散列算法来定位 segments 数组的索引，必须保证 segments 数组的长度是 2 的 N 次方。

2. 初始化 segmentShift 和 segementMask

  这两个全局变量需要在定位 segment 时的散列算法里使用，sshift 等于 ssize 从 1 向左移位的次数。segmentShift 等于 32 减 sshift。segmentMask 是散列运算的掩码，等于 ssize 减 1。

3. 初始化每个 segment

## 定位 Segment

在插入和获取元素的时候，必须先通过散列算法定位到 Segment。ConcurrentHashMap 会对元素的 hashCode 进行一次再散列。

```Java
private static int hash(int h) {
  h += (h << 15) ^ 0xffffcd7d;
  h ^= (h >>> 10);
  h += (h << 3);
  h ^= (h >>> 6);
  h += (h << 2) + (h << 14);
  return h ^ (h >>> 16);
}
```

之所以进行再散列，目的是减少散列冲突、使元素能够均匀地分布在不同的 Segment 上，从而提高容器的存取效率。

ConcurrentHashMap 通过以下散列算法定位 Segment：
```Java
final Segment<K,V> segmentFor(int hash) {
  return segments[(hash >>> segmentShift) & segmentMask];
}
```

## 操作

### get 操作

Segment 的 get 操作十分简单和高效。先经过一次再散列，然后使用这个散列值通过散列运算定位到 Segment，再通过散列算法定位到元素，代码如下：

```Java
public V get(Object key) {
  int hash = hash(key.hashCode());
  return segmentFor(hash).get(key,hash);
}
```

get 操作的高效之处在于整个 get 过程不需要加锁，除非读到的值是空才会加锁重读。

ConcurrentHashMap 的 get 操作不需要加锁的原因是：在 get 方法里将要使用的共享变量都定义为 volatile 类型，如用于统计当前 Segment 大小的 count 字段和用于存储值的 HashEntry 的 value。
被 volatile 修饰的变量，能够保持线程之间的可见性，能够被多线程同时读，并且保证不会读到过期的值，但是只能被单线程写，在 get 操作里只需要读不需要写共享变量 count 和 value，所以可以不用加锁。之所以不会读到过期的值，是因为根据 JMM 里的 happen before 原则，对 volatile 字段的写入操作先于读操作，即使两个线程同时修改和获取 volatile 变量，get 操作也能拿到最新的值。

定位 Segment 使用的是元素的 hashCode 通过再散列后得到的值的高位，而定位 HashEntry 直接使用的是再散列后的值。其目的是避免两次散列后产生的值一样，虽然元素在 Segment 里散列开了，但是却没有在 HashEntry 里散列开了。

```Java
(hash >>> segmentShift) & segmentMask; //定位 Segment 所使用的 hash 算法
int index = hash & (tab.length - 1); //定位 HashEntry 所使用的 hash 算法
```

### put 操作

由于 put 方法里需要对共享变量进行写入操作，所以必须加锁。put 方法首先定位到 Segment，然后在 Segment 里进行插入操作。插入操作需要经历两个步骤，第一步判断是否需要对 Segment 里的 HashEntry 数组进行扩容，第二步定位添加元素的位置，然后将其放在 HashEntry 数组里。

ConcurrentHashMap 在插入**前**会判断是否超过容量，如果超过阈值，则对数组进行扩容。而 HashMap 则是在插入元素**后**判断元素是否需要扩容，但是有可能扩容后没有新元素插入，这时 HashMap 就进行了一次无效的扩容。

扩容时，ConcurrentHashMap 不会对整个容器进行扩容，而只对某个 Segment 进行扩容。

### size 操作

如果要统计整个 ConcurrentHashMap 里元素的大小，就必须统计所有 Segment 里元素的大小后进行求和。Segment 里的全局变量 count 是一个 volatile 变量。

因为在累加 count 操作过程中，之前累加的 count 发生变化的几率特别小，所以 ConcurrentHashMap 的做法是先尝试 2 次通过不锁定 Segment 的方式来统计各个 Segment 大小，如果统计的过程中，容器的 count 发生了变化，则再采用加锁的方式来统计所有 Segment 的大小。

在 put、remove 和 clear 方法里将变量 modCount 进行加1，这样就可判断容器的 count 是否发生了改变。
