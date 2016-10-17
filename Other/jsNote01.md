# JS Note 01

## Base

* 尝试访问未声明的变量会抛出错误，但是通过查询 window 对象，可以知道某个未声明的变量是否存在。

```JavaScript
//这里会抛出异常，因为oldValue未定义
var newValue = oldValue;

//这里不会抛出异常，因为这是一次属性查询
//newValue 的值是 undefined
var newValue = window.oldValue;
ls
```

* JS 是单线程语言，但它允许通过设置超时时间和间歇时间值来调度代码在特定的时间就执行一次代码。

* cloneNode()：Node克隆
  * 深复制（true）：复制节点及整个子节点树
  * 浅复制（false）：只复制节点本身

## 正则表达式

### 修饰符

* g：global 全文搜索，不添加的话，搜索到第一个匹配停止
* i：ignore case 忽略大小写，默认大小写敏感
* m：multiple line 多行搜索
