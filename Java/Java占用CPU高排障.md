# 方法

* 找到占用CPU的进程
* 根据进程找到占用CPU的线程
* 将线程号转16进制
* 打印堆栈

# 过程及命令

* 通过top命令找到占用CPU高的Java进程，例如2793。可通过ps aux | grep PID确认。
* 通过命令ps -mp PID -o THREAD,tid,time | sort -rn，例如：ps -mp 2793 -o THREAD,tid,time | sort -rn。查看线程列表。
* 将线程号转为16进制，命令printf "%x\n" TID，例如printf "%x\n" 3626，输出e18
* 打印堆栈，命令jstack PID |grep TID -A 30，例如jstack 2793 |grep e18 -A 30
