# JVM调优标志

JVM主要接受两类标志：布尔标志、附带参数的标志  

布尔标志语法：**-XX:+FlagName 表示开启， -XX:-FlagName表示关闭**  

附带参数的标志语法：**-XXX:FlagName=value** 表示将flagname的值设置为value，其中value为任意值。  


## Client、Server虚拟机  

Windows上运行的32位（无论机器上CPU的个数），以及单CPU机器上运行的任何32位JVM，都是Client类机器。所有其它机器（所有64位）认为Server机器。  

## 对象提升（Promotion）规则

虚拟机给每个对象定义了一个对象年龄（Age）计数器，对象在Eden出生并经过第一次Minor GC后仍然存活，并且能被Survivor容纳的话，将到移动到Survivor空间，将
将对象年龄设置为1.对象在Survivor中每熬过一次Minor GC，年龄就会增加1岁，当年龄增加到一定程序（默认为15）就会被晋生到老年代中。对象晋生对老年代的年龄阈值，可以通过选项 **-XX:MaxTenuringThreshold** 来设置。

针对不同年龄段的对象分配原则如下：

- 1、对象优先分配在Eden区，如果Eden区没有足够的空间时，虚拟机执行一次Minor GC。
- 2、大对象直接进入老年代（大对象是指需要大量连续内存空间的对象），G1 GC中，占用连续两个Region为大对象。这样做的目的是避免在Eden区和两个Survivor区之间发生大量的内存拷贝。（年轻代采用复制算法收集内存）
- 3、长期存活的对象进入老年代中，当年代达到设置的阈值，对象就进入老年代。
- 4、动态判断对象的年龄，如果Survivor区中相同年龄的所有对象大小的总和大于Survivor空间的一半，年龄大于或等于该年龄的对象可以直接进入老年代。
- 5、空间分配担保，每次进行Minor GC时，JVM会计算Survivor区移至老年区的对象的平均大小，如果这个值大于老年区的剩余值大小则进行一次Full GC，如果小于则进入检查HandlePromotionFailure逻辑，如果判断逻辑为true，则只进行MinorGC，如果是False，则进行FullGC。


