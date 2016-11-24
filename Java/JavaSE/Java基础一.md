# Java基础一

  **一切都是对象**

## 存储数据

在Java中有五个地方可以存储数据

  * 寄存器：最快的存储区，位于不同于其他存储区的地方——处理器内部。存储器的数量及其有限，所以寄存器根据需求进行分配。

  * 堆栈：位于通用RAM(随机访问存储器)中，但通过移动 *堆栈指针* 可以从处理器那里获得直接支持。堆栈指针若向下移动，则分配新的内存；若向上移动，则释放那些内存。某些Java数据存储在堆栈中——特别是 **对象引用**，但是Java对象不存储于其中。

  * 堆：一种通用的内存池（位于RAM区），用于存放 **所有的Java对象**。堆不同于堆栈的好处在于：编译器不需要知道存储的数据在堆里存活多长时间。用堆进行储存分配和清理可能比堆栈进行储存分配需要更多的时间。

  * 常量存储：常量值通常直接存放在程序代码内部，这样做是安全的，因为它们永远不会被改变。

  * 非RAM存储：如果数据完全存活于程序之外，那么它可以不受程序的任何控制，在程序没有运行时也可以存在。其中两个基本的例子是： *流对象* 和 *持久化数据*。在流对象中，对象转化成字节流，通常被发送给另一台机器。在持久化数据中，对象被存放于磁盘上。

## 基本类型

<table border="1">
<tr>
  <th>基本类型</th><th>大小</th><th>最小值</th><th>最大值</th>
</tr>
<tr>
  <td>boolean</td><td>-</td><td>-</td><td>-</td>
</tr>
<tr>
  <td>char</td><td>16-bit</td><td>Unicode 0</td><td>Unicode 2^16 - 1</td>
</tr>
<tr>
  <td>byte</td><td>8 bits</td><td>-128</td><td>+127</td>
</tr>
<tr>
  <td>short</td><td>16 bits</td><td>-2^15</td><td>+2^15 - 1</td>
</tr>
<tr>
  <td>int</td><td>32 bits</td><td>-2 ^ 31</td><td>+2^31 - 1</td>
</tr>
<tr>
  <td>long</td><td>64 bits</td><td>-2^63</td><td>+2^63 - 1</td>
</tr>
<tr>
  <td>float</td><td>32 bits</td><td>IEEE754</td><td>IEEE754</td>
</tr>
<tr>
  <td>double</td><td>64 bits</td><td>IEEE754</td><td>IEEE754</td>
</tr>
</table>

PS: **boolean**类型所占存储空间的大小没有明确指定，仅定义为能够取字面值 **true** 和 **false**。

### 高精度数字

Java提供了两个用于高精度计算的类：**BigInteger** 和 **BigDecimal**。

* BigInteger支持任意精度的整数。

* BigDecimal支持任何精度的[定点数](https://baike.baidu.com/view/686808.htm)。
