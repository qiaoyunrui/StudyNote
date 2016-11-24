# Java基础四

## String

当执行这样一句话`String  s = "Hello";`时：

* JVM 首先查找对象池中是否存放有该String对象"Hello"，如果有，则返回已有的String对象地址。

* 如果对象池中没有该String对象，JVM则在堆中创建新的String对象，将其引用返回，同时把该引用添加到对象池中。

### String比较

* equals() - 比较两个字符串值是否相等

* == - 比较两个字符串对象是否引用同一个引用
