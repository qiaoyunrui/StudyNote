# split()函数解析(一)

## 起源

  突然研究split()函数是有一定原因的，昨天晚上有个厉害的学长在实验室的群里抛了这样一个问题：

  > 假设存在一个数组 array ={"AB", "12"},还存在一个字符串string = abcAB0123，有一个函数f(String s)，
使得 {"abc", "AB", "0"， "12", "3"} == f(string)。也就是把string按array中的元素拆分。求解这个·f函数。
ps：string.split("AB|12")的到的结果是｛"abc", "0", "3"｝，不满足条件。

  既然string.split()无法实现，那么看一下split()的源码是如何实现的，对其进行改造就可以了。

## 源码追踪

首先是String.split(String regex)，它的实现是这样的：
```java
public String[] split(String regex) {
    return split(regex, 0);
}
```

继续追踪，看一下String.split(String regex, int limit)的内部是如何实现的：
```java
/**
* @param  regex
*         the delimiting regular expression
* 这里的regex可以是一个正则表达式
* @param  limit
*         the result threshold, as described above
* 限制切割字符串的段数
* @return  the array of strings computed by splitting this string
*          around matches of the given regular expression
*
* @throws  PatternSyntaxException
*          if the regular expression's syntax is invalid
*/
public String[] split(String regex, int limit) {
    /* fastpath if the regex is a
     (1)one-char String and this character is not one of the
        RegEx's meta characters ".$|()[{^?*+\\", or
     (2)two-char String and the first char is the backslash and
        the second is not the ascii digit or ascii letter.
     */
    char ch = 0;
    if (((regex.value.length == 1 &&
         ".$|()[{^?*+\\".indexOf(ch = regex.charAt(0)) == -1) ||
         (regex.length() == 2 &&
          regex.charAt(0) == '\\' &&
          (((ch = regex.charAt(1))-'0')|('9'-ch)) < 0 &&
          ((ch-'a')|('z'-ch)) < 0 &&
          ((ch-'A')|('Z'-ch)) < 0)) &&
        (ch < Character.MIN_HIGH_SURROGATE ||
         ch > Character.MAX_LOW_SURROGATE))
    {
        int off = 0;  //偏移量
        int next = 0; //下一次切割的地方
        boolean limited = limit > 0;  //判断是否有限制，如果limit = 0则表示无限制
        ArrayList<String> list = new ArrayList<>(); //盛装切割之后的字符串
        while ((next = indexOf(ch, off)) != -1) { //offset之后还有该字符
            if (!limited || list.size() < limit - 1) {
                list.add(substring(off, next));
                off = next + 1;
            } else {    // last one
                //assert (list.size() == limit - 1);
                list.add(substring(off, value.length));
                off = value.length;
                break;
            }
        }
        // If no match was found, return this
        if (off == 0)
            return new String[]{this};

        // Add remaining segment
        if (!limited || list.size() < limit)
            list.add(substring(off, value.length));

        // Construct result
        int resultSize = list.size();
        if (limit == 0) {
            while (resultSize > 0 && list.get(resultSize - 1).length() == 0) {
                resultSize--;
            }
        }
        String[] result = new String[resultSize];
        return list.subList(0, resultSize).toArray(result);
    }
    return Pattern.compile(regex).split(this, limit);
}
```
首先看一下函数第一行的注释：
> fastpath if the regex is a
 (1)one-char String and this character is not one of the
    RegEx's meta characters ".$|()[{^?*+\\", or
 (2)two-char String and the first char is the backslash and
    the second is not the ascii digit or ascii letter.

**翻译过来就是：**

1. 当regex是一个字符的字符串并且这个字符不是正则表达式中的元字符或者

2. regex是两个字符的字符串并且第一个字符是'/'，第二个字符不是数字或者字母的时候（其实这里也相当于一个字符，使一些转义字符）

  本方法的时间复杂度会很低（fastpath）？

那么对应的代码就是：
```java
if (((regex.value.length == 1 &&
             ".$|()[{^?*+\\".indexOf(ch = regex.charAt(0)) == -1) ||
             (regex.length() == 2 &&
              regex.charAt(0) == '\\' &&
              (((ch = regex.charAt(1))-'0')|('9'-ch)) < 0 &&
              ((ch-'a')|('z'-ch)) < 0 &&
              ((ch-'A')|('Z'-ch)) < 0)) &&
            (ch < Character.MIN_HIGH_SURROGATE ||
             ch > Character.MAX_LOW_SURROGATE))
```

上面的这个if语句就是是否符合fastpath的条件：

* `(regex.value.length == 1 && ".$|()[{^?*+\\".indexOf(ch = regex.charAt(0)) == -1)` 长度为1并且不是正则表达式中的元字符. $ | ( ) [ { ^ ? * + \

* `(regex.length() == 2 && regex.charAt(0) == '\\' && (((ch = regex.charAt(1))-'0')|('9'-ch)) < 0 && ((ch-'a')|('z'-ch)) < 0 && ((ch-'A')|('Z'-ch)) < 0)` 长度为2，第一个字符是'\' 并且第二个字符不是字母或者数字。`((ch-'A')|('Z'-ch)) < 0`这个判断是否为字符的方式很nice，很独特！

* `(ch < Character.MIN_HIGH_SURROGATE || ch > Character.MAX_LOW_SURROGATE)` 这是一个必要条件，字符必须在范围内

再继续往下看，定义了几个变量，分别是：
```java
int off = 0;  //偏移量
int next = 0; //下一次出现目标字符的位置
boolean limited = limit > 0;  //是否有限制
ArrayList<String> list = new ArrayList<>(); //盛装切割之后的字符片段
```

然后就是一个while循环：`while ((next = indexOf(ch, off)) != -1)`

出现了一个indexOf(int ch, int fromIndex)方法，这个方法的英文解释是这样的：
> Returns the index within this string of the first occurrence of the specified character, starting the search at the specified index.

翻译一下就是：返回目标字符在字符串中`fromIndex`位置之后**第一次** 出现的位置，如果没有的话就返回-1。

所以这个while循环就是当剩下的字符串还有目标字符的话，就会继续循环。

接下来的部分就简单多了，根据offset和目标出现的下一个位置使用substring函数对字符串进行切割，并把切割下的部分添加到list中去，如果这是目标的最后一次出现位置或者超出limit的范围，直接把字符串最后的部分添加到list中。注意，每次循环都要调整偏移量，如果不是最后一次循环，令off = next + 1。

循环结束之后，如果off仍然是0，说明没有匹配到，直接返回就好。然后把list转换为字符串数组返回就可以了。

## 更加深入

你以为就这样结束了？

别忘了，我们上面讨论的只是fastpath情况，也就是用一个字符切割字符串。

`Pattern.compile(regex).split(this, limit)` 这是另一种情况，请等待我的下回讲解。

PS：那位学长已经解决了这个那个问题，方法是：使用构词器。
