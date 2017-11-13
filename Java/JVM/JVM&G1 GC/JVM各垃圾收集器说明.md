### GC代码示例

<code>
Java HotSpot(TM) 64-Bit Server VM (25.60-b23) for windows-amd64 JRE (1.8.0_60-b27), built on Aug  4 2015 11:06:27 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16722792k(7442296k free), swap 33443724k(22714744k free)  
CommandLine flags: -XX:InitialHeapSize=1073741824 -XX:MaxHeapSize=1073741824 -XX:+PrintGC -XX:+PrintGCDateStamps -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC 
Heap  
 PSYoungGen<sup>1</sup>     total 305664K, used 83889K [0x00000000eab00000, 0x0000000100000000, 0x0000000100000000)  
  eden space 262144K, 32% used [0x00000000eab00000,0x00000000efcec610,0x00000000fab00000)  
  from space 43520K, 0% used [0x00000000fd580000,0x00000000fd580000,0x0000000100000000)  
  to   space 43520K, 0% used [0x00000000fab00000,0x00000000fab00000,0x00000000fd580000)  
 ParOldGen       total 699392K, used 0K [0x00000000c0000000, 0x00000000eab00000, 0x00000000eab00000)  
  object space 699392K, 0% used [0x00000000c0000000,0x00000000c0000000,0x00000000eab00000)  
 Metaspace       used 5316K, capacity 5566K, committed 5888K, reserved 1056768K  
  class space    used 591K, capacity 667K, committed 768K, reserved 1048576K  
</code>  
<br>
<br>
<br>
<br>
<br>  
<strong>1-GC的命名</strong>：因GC收集器不同而不同，具体如下：

* 串行收集器：DefNew，使用-XX:+UseSerialGC（年轻代、老年代使用串行收集器）输出的。
* 并行收集器：ParNew，使用-XX:+UseParNewGC（年轻代使用并行收集器，老年代使用串行收集器），或使用-XX:UseConcMarkSweepGC（年轻代使用并行收集器，老年代使用CMS）运行后输出的。
