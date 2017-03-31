# Java 并发容器和框架

## ConcurrentHashMap 的实现原理

ConcurrentHashMap 是线程安全且高效的 HashMap。

### 使用 ConcurrentHashMap 的必要性

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
