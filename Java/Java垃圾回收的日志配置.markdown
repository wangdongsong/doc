# Java垃圾回收的日志配置

标签（空格分隔）： GC JVM

---

##Heap基础
|eden| s0 | s1 | tenured|
|----|----|----|--------|
|新生代|新生代|新生代|老年代  | 

##GC相关参数  

* -XX:+PrintGC：JVM启动之后，只要遇到GC，就会打印日志  
* -XX:+PrintGCDetails：打印的详细的垃圾回收日志，同时，在JVM退出前打印Heap的详细信息。  
* -XX:+PrintHeapAtGC：会在每次GC前后分别打印堆的信息，如同-XX:+PrintGCDetails的最后输出一样，使用此参数，可以分别看到GC前和后的信息，观察GC对堆空间的影响。  
* -XX:+PrintGCApplicationConcurrentTime：由于GC会引起应用程序停顿，因此，可能需要特别关注应用程序的执行时间和停顿时间。可使用此参数打印应用程序的执行时间。  
* -XX:+PrintReferenceGC：此参数可以跟踪系统内的软引用、弱引用、虚引用和Finallize队列。  
* -Xloggc：指定GC的log的输出目录，例如：-Xloggc:log/gc.log。  





