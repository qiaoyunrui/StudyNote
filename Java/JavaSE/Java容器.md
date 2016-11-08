# Java容器

Java容器类库类库得用途是“保存对象”。

## Collection

一个独立元素的序列。

Collection接口中提供的方法有

* size():int
* isEmpty():boolean
* contains(Object):boolean
* iterator(): Iterator<E>
* toArray():Object[]
* toArray(T[]):T[]
* add(E):boolean
* remove(Object):boolean
* clear():void
* stream():Stream<E>
* ...

### List


List本质上是一个线性表。
List接口在Collection接口的基础上添加了大量的方法，使得可以在List中间插入和移除元素以及其他操作。

新加的方法有：

* sort(Comparator<? extends E>):void
* get(int):E
* set(int,E):E
* add(int,E):void
* remove(int):E
* indexOf(Object):int
* subList(int,int):List<E>
* ...

有两种基本类型的List：

* ArrayList - 长于随机访问元素，但是在List的中间插入和移除元素时较慢。内部实现是数组方式。

* LinkedList - 在随机访问方面相对比较慢，但是它的插入和移除元素操作比较快。

## Map

一组成对的“键值对”对象，允许你使用键来查找值。
