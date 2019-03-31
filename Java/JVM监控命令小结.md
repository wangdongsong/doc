# 总体

|场景|命令|
|----|----|
|内存不足，OutOfMemoryError|jmap|
|内存不足，OutOfMemoryError、GC频繁、服务超时、出现长尾响应现象|jstat|
|服务超时、线程卡死、线程死锁、服务器负载高|jstack|
|查看或修改Java进程的环境变量和Java虚拟机变量|jinfo|
|查找Java进程ID|jps|
|分析jmap产生的Java堆的快照|jhat|

## jmap

### 空间占用信息
jmap -histo:live PID

### 堆概要信息
jmap -heap PID

## jstat

jstat利用JVM内建的指令对Java应用程序的资源和性能进行实时的命令行监控，包括堆大小、垃圾回收状况

jstat -gcutil PID 5000 10

## jstack

jstack输出Java进程的堆栈快照信息，可以看线程的执行状态、正在执行的任务

jstack PID
