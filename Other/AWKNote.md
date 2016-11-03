# AWK Note

## 介绍

AWK是一门解释型的编程语言。

## 程序结构

### BEGIN 语句块

语法
```shell
BEGIN {awk-commands}
```

BEGIN语句块在程序开始时执行，它只执行一次，在这里可以初始化变量。BEGIN时AWK的关键字，因此它必须为大写，注意，这个语句块是可选的。

### BODY语句块

语法
```shell
/pattern/ {awk-commands}
```

BODY语句块中的命令对输入的每一行执行，可以通过提供模式来控制这种行为。注意，BODY语句块没有关键字。

### END语句块

语法
```shell
END {awk-commands}
```

END语句块在程序的在最后执行，END是AWK的关键字，必须为大写，它也是可选的。

## 基础语法

* Base
```shell
awk [options] file
```
emp:
```shell
awk '{print}' marks.txt
```

* AWK程序文件
```shell
awk [options] -f file
```
emp:
```shell
awk -f command.awk marks.txt
```

* ```-v```变量赋值选项
emp:
```shell
awk -v name="Jerry" 'BEGIN{printf "Name = %s\n",name}'
```

## 内建变量

* ARGC - 命令行参数个数

* ARGV - 命令行参数数组

* ENVIRON - 环境变量

* CONVFMT - 数字的约定格式
  代表了数字的约定格式，默认值是%.6g

* FILENAME - 当前文件名

* FS - 输入字段得分隔符
  代表了输入字段得分隔符，默认值是**空格**，可以通过```-F```选项在命令行选项中修改

* NF - 字段数目
  代表了当前行中的字段数目

* NR - 行号

* FNR - 行号（相对于当前文件）
