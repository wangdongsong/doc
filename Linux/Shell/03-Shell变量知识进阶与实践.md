## Shell中特殊变量

### Shell中的特殊位置参数变量

* $0：获取当前执行的Shell脚本的文件名，如果执行脚本包含路径，则$0包括脚本路径。
* $n：获取当前执行Shell脚本的第n个参数值，n=1..9，当n为0时表示脚本的文件名；如果n大于，则用大括号括起来，例如${10}，接的参数以空格分隔。
* $#：获取当前执行Shell脚本后面接的参数的总个数。
* $*：获取当前Shell脚本所有传参的参数，不加引号和$@相同，如果给$*加上双引号，例如"$*"，则表示将所有的参数视为单个字符串，相当于"$1 $2 $3"。
* $@：获取当前Shell脚本所有传参的参数，不加引号和$*相同；如果给$@加上双引号，例如"$@",则表示将所有的参数视为不同的独立字符串，相当于"$1" "$2" "$3"。

**备注：** $*和$@不加引号两者无区别，加引号时$*为一个串，$@为单个串。

```Linux
dirname $0 #文件路径
basename $0 #文件名
[ $# -ne 2 ] && { #参数个数不等于2
    echo "must two args"
    exit 1
}
```

### Shell进程中的特殊状态变量

* $?：获取执行上一个指令的状态返回值（0：成功， 非零为失败），最常用的变量。
* $$：获取当前执行的Shell脚本的进程号（PID）。
* $!：获取上一个在后台工作的进程的进程号（PID）。
* $_：获取在此之前执行的命令或脚本的最后一个参数。

$?返回值的用法：
* 判断命令、脚本或函数等程序是否执行成功
* 若在脚本中调用exit 数字，则返回的这个数字给$?变量
* 如果是在函数里，则通过return 数字，把这个数字以函数返回值的形式传给$?

$$：获取当前进程号，示例：多次执行某一个脚本后进程只有一个

```Linux
#!/bin/sh
pidpath=/tmp/tmppid.pid #记录进程号
if [ -f "$pidpath" ] #判断文件是否存在
    then
        #echo "kill" `cat $pidpath` 
        kill `cat $pidpath` >/dev/null 2>&1 #杀死进程
	       rm -f $pidpath #删除文件
fi
echo $$ > $pidpath #重写文件
sleep 60 #暂停60s，模拟守护进程

```

## bash Shell内置变量命令

### echo 屏幕上输出信息

参数选项：

* -n：不换行输出内容
* -e：解析转义字符
* \n：换行
* \r：回车
* \t：制表符
* \b：退格
* \v：纵向制表符

### eval
### exec
### read
### shift
### exit

## Shell变量子串知识及实践

### 变量子串介绍

* ${parameter}：返回变量内容
* ${#parameter}：返回变量内容的长度（按字符），也适用特殊字符
* ${parameter:offset}：在变量中，从位置offset之后开始提取子串到结尾
* ${parameter:offset:length}：在变量中，从位置offset之后开始提取长度length的子串
* ${parameter#word}：从变量开头开始删除最短匹配的word子串
* ${parameter##word}：从变量开头开始删除最长匹配的word子串
* ${parameter%word}：从变量结尾开始删除最短匹配的word子串
* ${parameter%%word}：从变量结尾开始删除最长匹配的word子串
* ${parameter/pattern/string}：使用string代替第一个匹配的pattern
* ${parameter//pattern/string}：使用string代替所有匹配的pattern

**变量长度**

```Linux
[user@host shell]$ str="I am lance"
[user@host shell]$ echo ${#str}
10
[user@host shell]$ echo $str | wc -l
1
[user@host shell]$ echo ${str} | wc -l
1
[user@host shell]$ expr length "$str"
10
[user@host shell]$ echo "$str" | awk '{print length($0)}'
10
[user@host shell]$ echo ${#str} #性能最好
10
```

## Shell特殊扩展变量

* ${parameter:-word}：如果parameter的变量值为空或未赋值，则返回word字符串并替代变量的值
* ${parameter:=word}：如果parameter的变量值为空或未赋值，则设置该变量的值为word
* ${parameter:?word}：如果parameter的变量值为空或未赋值，则word字符串被作为标准错误输出，否则输出变量的值，可用于变量未定义提示错误
* ${parameter:+word}：如果parameter的变量值为空或未赋值，什么都不做，否则word字符串将变量的值

**注意：**冒号可以取消

```Linux
#当path变更未定义，提示not defined
[user@host ~]$ echo ${path:?not defined}
-bash: path: not defined

#生产案例，删除七天前的数据
#当path未定义时，删除/tmp目录下的文件，不使用此方法时，会因path未定义而异常
#-type f，表示只找file，文件类型的，目录和其他字节啥的不要 
#find ... -exec rm {} \; 
#find ... | xargs rm -rf 
#
#两者都可以把find命令查找到的结果删除，其区别简单的说是前者是把find发现的结果一次性传给exec选项，这样当文件数量较多的时候，就可能会出现“参数太多”之#类的错误，相比较而言，后者就可以避免这个错误，因为xargs命令会分批次的处理结果。这样看来，“find ... | xargs rm -rf”是更通用的方法，推荐使用！

find ${path-/tmp} -name "*.tar.gz" -type f -mtime +7 | xargs rm -rf
```
