# Java基础三

## final的用法

* 修饰变量，该变量不可被修改

  对于基本基本数据类型，`final`使数值恒定不变；对于对象引用，`final`使引用恒定不变。一旦引用被初始化指向一个对向，就无法再把它改为指向一个对象。然而对象本身却是可以被修改的，

  一个既是`static`又是`final`的域只占据一段不能改变的空间。

  方法参数用`final`修饰，表示在方法中更改参数引用所指向的对象。

* 修饰类，该类不可被继承

* 修饰方法，该方法不可被重写

## 装箱与拆箱

首先看这样的代码：
```Java
Integer i1 = 32;
Integer x1 = 32;
System.out.println(i1 == x1);
Integer i2 = 132;
Integer x2 = 132;
System.out.println(i2 == x2);
```

输出结果如下：
```Java
true
false
```

解释如下：

**初始值在 -128 -- 127 之间的值，它们被装箱为Integer对象后，会在内存中被重用。超过这个值的时候每次装箱是会产生新的对象。**

### 为重载带来的问题

先看下面的代码：
```Java
public class main{

    public static void hello(Integer x) {
        System.out.println("Integer");
    }

    public static void hello(long x) {
        System.out.println("long");
    }

    public static void hello(Long x) {
        System.out.println("Long");
    }

    public static void main(String[] args) {
        int i = 5;
        hello(5);
    }
}
```

打印结果为：
```
long
```

**在这种情况下编译器选择的是加宽操作而不是装箱。**

测试结果（优先级排序）：

long > float > Integer > Long(运行出错) > char(编译报错)

## 数组

当创建一个数组对象时，实际上就是创建了一个 **引用数组**，并且每个引用都会自动被初始化为一个特定值。

### 使用`new`为数组分配空间

```Java
int[] nums = new int[10];
```

**注意：如果是对象数组的话，数组中的每个元素都要初始化。** 例如：

```Java
Object[] objs = new Object[10];
System.out.println(objs[0]);
for (int i = 0; i < 10; i++) {
    objs[i] = new Object();
}
System.out.println(objs[0]);
```

输出结果为：

```java
null
java.lang.Object@12133b1
```

### 二维数组的初始化

```java
int[][] var0 = new int[][]{{1, 2, 3}, {4, 5, 6}};
int[][] var1 = new int[2][];
var1[0] = new int[]{1, 2};
var1[1] = new int[]{3, 4};
```

### Arrays类

常用的方法有：

* equals() - 比较两个数组是否相等

* fill() - 以指定的内容填充数组

* sort() - 对数组进行排序

* toString() - 输出数组

* copyOfRange() - 将指定数组的指定范围复制到另一个数组

* binarySearch() - 二分查找
