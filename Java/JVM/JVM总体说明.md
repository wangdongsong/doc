# JVM调优标志

JVM主要接受两类标志：布尔标志、附带参数的标志  

布尔标志语法：**-XX:+FlagName 表示开启， -XX:-FlagName表示关闭**  

附带参数的标志语法：**-XXx:FlagName=value** 表示将flagname的值设置为value，其中value为任意值。  


## Client、Server虚拟机  

Windows上运行的32位（无论机器上CPU的个数），以及单CPU机器上运行的任何32位JVM，都是Client类机器。所有其它机器（所有64位）认为Server机器。  

