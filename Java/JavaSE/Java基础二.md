# Java基础二

## 操作符

* 将float或double转型为整型值时，总是会对该数字执行截尾，如果想要得到舍入的结果，就需要使用java.lang.Math中的round()方法。

* 如果对基本数据类型执行算术运算或位运算，只要类型比int小（即char、byte或short），那么在运算之前这些值会自动转换为int，所以最终得到的结果也是int。

* 通常表达式中出现的最大的数据类型决定了表达式最终结果的数据类型。例如：将一个float类型和double类型相乘，结果就是double类型。

## 访问权限

* 默认 - 包访问权限
* public - 公开
* protected - 继承访问权限
* private - 私有
