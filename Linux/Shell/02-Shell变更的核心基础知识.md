## 变量

默认情况下，Shell变更不区分变量类型，若有特殊需要指定Shell变量的类型，可以使用declare显示定义变量的类型。

变量类型：环境变更（全局变量）和普通变量（局部变量）

* 环境变更，也称全局变量，可以在创建它们的Shell及派生出来的任意子进各Shell中使用。环境变量又可分为自定义环境变量和bash内置的环境变量。
* 普通变量，也称局部变量，只能在创建它们的Shell函数或脚本 中使用。

## 普通变量

* 数字内容的变量定义可以不加引号
* 双引号：其它没有特别要求的字符串等定义最好都加上双引号
* 单引号：如果真的需要原样输出就加单引号，定义变量加双引号是常用的使用场景。
* 反引号：引用命令，可以使用反引号``或$()

当变量后面连接有其它字符的时候，必须给变量加上大括号{}，例如${dbname}_tname。

```
[user@host shell]$ echo "Today is $(date +%F)"
Today is 2017-12-26
[user@host shell]$ echo "Today is `date +%F`"
Today is 2017-12-26
[user@host shell]$ echo "Today is 'date +%F'"
Today is 'date +%F'

[user@host shell]$ ETT="lance"
[user@host shell]$ echo $ETT | awk '{print $0}'
lance
[user@host shell]$ echo "$ETT" | awk '{print $0}'
lance
[user@host shell]$ echo `pwd` | awk '{print $0}'
/wds/lance/shell
[user@host shell]$ echo `$ETT` | awk '{print $0}'
-bash: lance: command not found

```

**说明：** awk比较特殊，通常先使用echo加符号输出变量，然后通过管道给awk。
