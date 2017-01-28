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
