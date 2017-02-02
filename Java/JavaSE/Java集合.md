# Java集合

## Iterable 接口

* Iterator<T> iterator() - 获取迭代器

* void forEach(Consumer<? super T> action) - 遍历，接受一个lambda表达式 [Java 8]

* Spliterator<T> spliterator() - 获取一个并行迭代器 [Java 8]

## Collection 接口

* int size()
* boolean isEmpty()
* boolean contains(Object o)
* Object[] toArray()
* <T> T[] toArray(T[] a)
* boolean add(E e)
* boolean remove(Object o)
* void clear()
* ...

## List 接口

* void sort(Comparator<? super E> c)
* E get(int index)
* E set(int index, E element)
* void add(int index, E element)
* E remove(int index)
* int indexOf(Object o)
* ListIterator<E> listIterator()
* List<E> subList(int fromIndex, int toIndex)
* ...

## ArrayList

* initialCapacity - 初始容量（如果不指定，默认是10）

* transient Object[] elementData - 存储数据的数组
  `transient` 关键字的作用是：**不** 序列化这个数据。

ArrayList内部是通过数组方式实现的。

ArrayList每次扩容(`grow(int)`)都是将自己的容量扩展到1.5倍，每次添加数据(`add(E)`，`add(int, E)`)的时候，都会进行判断是否需要扩容。

源码如下：
```Java
/**
 * Increases the capacity to ensure that it can hold at least the
 * number of elements specified by the minimum capacity argument.
 *
 * @param minCapacity the desired minimum capacity
 */
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

在ArrayList的remove操作中，总是会把要删除的那个位置设置为null，是为了让GC对空间进行回收。

`elementData[--size] = null; // clear to let GC do its work`

## LinkedList

就是我们所说的链表，并且是双向链表。

结点(`Node`)的数据结构：
```Java
private static class Node<E> {
    E item;
    Node<E> next;   //指向下一个Node
    Node<E> prev;   //指向上一个Node

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

LinkedList有如下几个重要的属性：

```Java
transient int size = 0;
transient Node<E> first;    //指向第一个结点
transient Node<E> last;     //指向最后一个结点
```

LinkedList还实现了队列(`queue`)的一些操作，例如：`pop()`、`push()`等等
