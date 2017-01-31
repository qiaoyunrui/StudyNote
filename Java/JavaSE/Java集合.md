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
  `transient` 关键字的作用是：不序列化这个数据。

ArrayList内部是通过数组方式实现的，
