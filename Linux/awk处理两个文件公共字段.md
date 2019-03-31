在文件score.txt中存有如下数据：（姓名 分数）

lisi 88

bokeyuan 97

zhangsan 77

wangwu 89

hongliu 92

zhanghua 97

在文件student.txt中存有：

zhangsan

hongliu

使用awk, cut, grep, find等任意shell脚本，输出student.txt所有学生的分数，即输出：

zhangsan 77

hongliu 92

如果只在score.txt中处理，可以很容易得写出

awk ' { if ($1 == "zhangsan") print $0} ' score.txt

但是结合另外一个文件，该怎么处理呢？

求人不如求自己，到处找了找处理方法，可以使用awk来处理多个文件，答案如下：

 awk  ' {if (ARGIND==1) grade[$1] = $0}  {if (ARGIND>1 && ($1 in grade)) print grade[$1]} ' grade.txt name.txt

简化版得写法为：

 awk 'ARGIND==1 {grade[$1]=$0}  ARGIND>1 && ($1 in grade) {print grade[$1]}' grade.txt name.txt

分析：

ARGIND==1处理第一个参数，即grade.txt文件

{grade[$1] = $0} 以grade.txt文件中的第一列为索引，将grade.txt中得内容存入grade数组中

if (ARGIND>1 && ($1 in grade)) print grade[$1] 如果处理的是第二个及以后的文件，即name.txt，检查第一列（姓名）是否在grade数组中，如果在，就打印以姓名为索引的grade信息。
