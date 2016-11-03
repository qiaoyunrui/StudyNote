# JavaScript 正则表达式

## RegExp 对象

JavaScript通过内置对象RegExp支持正则表达式

有两种方法实例化RegExp对象：

* 字面量
```JavaScript
var reg = /\bis\b/g;
```

* 构造函数
```JavaScript
var reg = new RegExp('\\bis\\b','g');
```

使用方法：
```JavaScript
'He is a boy. This is a dog. Where is she?'.replace(reg,'IS');
```

输出结果：
```
"He IS a boy. This IS a dog. Where IS she?"
```

构造函数的第二个参数以及字面量结尾的字符就是修饰符，修饰符有如下：

* g - global 全文搜索，不添加的话，搜索到第一个停止

* i - ignore case 忽略大小写，默认大小写敏感
