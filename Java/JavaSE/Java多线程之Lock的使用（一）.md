# Java多线程之Lock的使用（一）

Java多线程中，可以使用`synchronized`关键字实现线程之间同步互斥，JDK1.5中新增加了`ReentrantLock`类也可以达到同样的效果，并且更加强大。

## 如何使用

```javascript
class MyService {
   private var lock: Lock = ReentrantLock()
   fun testMethod() {
       lock.lock() //获取锁
       for (i in 1..5) {
           println("ThreadName is ${Thread.currentThread().name} $i")
       }
       lock.unlock()   //释放锁
   }
}
```

如代码所示，调用`ReentrantLock`对象的`lock()`方法获取锁，调用`unlock()`方法释放锁。

调用`lock.lock()`代码的线程就持有了“对象监视器”，其他线程就只有等待锁被释放时再次争抢。效果和`synchronized`关键字一样，线程之间执行的顺序时随机的。

## 使用Condition实现等待/通知

关键字`synchronized`与`wait()`和`notify()`/`notifyAll()`方法结合可以实现等待/通知模式，类`ReentrantLock`也可以实现同样的功能，但需要借助于`Condition`对象。

`Condition`类是JDK1.5中出现的技术，使用它有更好的灵活性，比如可以实现**多路通知**功能，也就是在一个`Lock`对象里面创建多个`Condition`（即对象监视器）实例，线程对象可以注册在指定的`Condition`中，从而可以有选择的进行线程通知。

在使用`notify()`方法进行通知的时候，被通知的线程是由JVM随机选择的。使用`ReentrantLock`结合`Condition`是可以实现“选择性通知”的。

`synchronized`相当于整个`Lock`对象中只有一个单一的`Condition`对象，所有线程都注册在它一个对象上。

实现代码如下：
```javascript
class MyService {

    private var lock: Lock = ReentrantLock()

    private var condition: Condition = lock.newCondition()

    fun await() {
        try {
            lock.lock()
            println("await时间为： ${System.currentTimeMillis()}")
            condition.await()
        } catch(e: InterruptedException) {
            e.printStackTrace()
        } finally {
            lock.unlock()
        }
    }

    fun signal() {
        try {
            lock.lock()
            println("signal时间为：${System.currentTimeMillis()}")
            condition.signal()
        } finally {
            lock.unlock()
        }
    }
}
```

`Object`类中的wait()方法相当于`Condition`类中的`await()`方法，`Object`类中的`notify`相当于`Condition`中的`signal()`。

## 使用多个Condition实现通知部分线程

实现代码如下：
```javascript
class MyService {

    private var lock: Lock = ReentrantLock()

    private var conditionA: Condition = lock.newCondition()
    private var conditionB: Condition = lock.newCondition()

    fun awaitA() {
        try {
            lock.lock()
            println("begin awaitA时间为：${System.currentTimeMillis()} ThreadName = ${Thread.currentThread().name}")
            conditionA.await()
            println("end awaitA时间为：${System.currentTimeMillis()} ThreadName = ${Thread.currentThread().name}")
        } catch(e: InterruptedException) {
            e.printStackTrace()
        } finally {
            lock.unlock()
        }
    }

    fun awaitB() {
        try {
            lock.lock()
            println("begin awaitB时间为：${System.currentTimeMillis()} ThreadName = ${Thread.currentThread().name}")
            conditionB.await()
            println("end awaitB时间为：${System.currentTimeMillis()} ThreadName = ${Thread.currentThread().name}")
        } catch(e: InterruptedException) {
            e.printStackTrace()
        } finally {
            lock.unlock()
        }
    }

    fun signalAll_A() {
        try {
            lock.lock()
            println("signalAll_A时间为：${System.currentTimeMillis()} ThreadName = ${Thread.currentThread().name}")
            conditionA.signalAll()
        } finally {
            lock.unlock()
        }
    }

    fun signalAll_B() {
        try {
            lock.lock()
            println("signalAll_B时间为：${System.currentTimeMillis()} ThreadName = ${Thread.currentThread().name}")
            conditionB.signalAll()
        } finally {
            lock.unlock()
        }
    }
}
```

## 公平锁与非公平锁

锁Lock分为“公平锁”和“非公平锁”，公平锁表示线程获取锁的顺序是按照线程加锁的顺序来分配的，即先来先得的`FIFO`顺序。而非公平锁就是一种获取锁的抢占机制，是随机获得锁的。

* 创建公平锁：
```javascript
var lock: Lock = ReentrantLock(true)
```
or
```javascript
var lock: Lock = ReentrantLock()
```

* 创建非公平锁：
```javascript
var lock: Lock = ReentrantLock(false)
```

## ReentrantLock一些常用的方法

* getHolderCount():int - 查询当前线程保持此锁定的个数

* getQueueLength():int - 获取正在等待获取此锁定的线程**估计数**

  比如有5个线程，一个线程首先执行`await()`方法，那么调用`getQueueLength()`方法的返回值是4，说明4个线程同时等待`lock`的释放。

* getWaitQueueLength(Condition):int - 返回等待与此锁定相关的给定条件`Condition`的线程**估计数**

  比如有5个线程，每个线程都执行了同一个`Condition`对象的`await()`方法，则调用`getWaitQueueLength()`方法时返回5

* hasQueuedThread(Thread):boolean - 查询指定的线程是否正在等待获取此锁定

* hasQueuedThreads():boolean - 查询是否有线程正在等待获取此锁定

* hasWaiters(Condition):boolean - 查询是否有线程正在等待与此锁定有关的`Condition`条件

* isFair():boolean - 判断是不是公平锁

* isHeldByCurrentThread():boolean - 查询当前线程是否保持锁定

* isLocked():boolean - 查询此锁定是否由任意线程保持

* lockInterruptibly():void - 如果当前线程未被中断，则获取锁定，如果已经被中断则出现异常

* tryLock():boolean - 仅在调用时锁定未被另一个线程保持的情况下，才获取该锁定

* tryLock(long,TimeUnit):boolean - 如果锁定在给定等待时间内没有被另一个线程保持，且当前线程未被中断，则获取该锁定

* awaitUnintterruptibly():void - 进入无法被打断的等待状态
