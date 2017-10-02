# Serviceability Agent

===

Hotspot SA由JDK直接提供，是一系列优秀的JavaAPI和工具的统称，可以直接调试运行着的Java进程和jvm的core文件。SA能检查JVM快照和core文件，实际上是一个静态
查看工具。SA链接到运行着的Java进程时，会停止当前进程，并检查该时间点的堆栈信息、线程运行情况、虚拟机内部的数据结构、类和方法信息等。并且在SA断开与Java
进程链接后，进程恢复运行状态。  

路径：JDK_HOME/lib目录下  

## 调试工具  

SA提供2个调试工具：
 
> 一个是图形化设计工具（HSDB：Hotspot Debugger）。  
> 一个是命令行工具（CLHSDB：Command-Line Hotspot Debugger）。  
