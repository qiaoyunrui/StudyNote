# Java多线程之Lock的使用（二）

类`ReentrantLock`具有完全互斥排他的效果，即同一时间只有一个线程在执行`ReentrantLock.lock()`方法后面的任务。这样做虽然保证了实例变量的线程安全性，但效率却是非常低下的。所以在JDK中提供了一种读写锁`ReentrantReadWriteLock`类，使用它可以加快运行速率，在某些不需要操作实例变量的方法中，完全可以使用读写锁`ReentrantReadWriteLock`来提升该方法的代码运行速度。

读写锁表示也有两个锁，一个是读操作相关的锁，也称为共享锁；另一个是写操作相关的锁，也叫排他锁。**多个读锁之间不互斥，读锁与写锁互斥，写锁与写锁互斥**。在没有线程`Thread`进行写入操作时，进行读取操作的多个`Thread`都可以获取读锁，而进行写入操作的`Thread`只有在获取写锁才能进行写入操作。即多个`Thread`可以同时进行读取操作，但是同一时刻只允许一个`Thread`进行写入操作。

## 如何使用

```javascript
class Service {
   private var lock: ReentrantReadWriteLock = ReentrantReadWriteLock()
   fun read() {
       try {
           try {
               lock.readLock().lock()
               println("获得此锁：${Thread.currentThread().name} ${System.currentTimeMillis()}")
               Thread.sleep(10000)
           } finally {
               lock.readLock().unlock()
           }
       } catch(e: InterruptedException) {
           e.printStackTrace()
       }
   }
}
```

上面代码显使用了读锁，相应的，写锁应该是：

```javascript
lock.writeLock().lock()

lock.writeLock().unlock()
```
