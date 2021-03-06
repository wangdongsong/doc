# 并发标记-清除垃圾收集器

该垃圾收集器官方称：基本上并发标记-清除垃圾收集器。该组垃圾收集器在新生代使用并发的标记-复制算法，有stop-the-world，而在老年代使用并发-清除算法。

该收集器的设计目标是收集老年代时避免长时间停顿。通过两个办法实现该目标，一是老年代没有压缩，使用空间列表方式管理回收的空间，二是分为标记、清除两  
个阶段。意味着该垃圾收集器不会显示在这两个阶段的停止的应用线程，但应该意识到它依旧会和应用线程竞争CPU时间，默认情况下，该算法使用的线程为主机物理  
CPU术数的四分之一。

通过如下命令行参数启用CMS收集器

```Java
java -XX:+UseConcMarkSweepGC com.mypackages.MyExecutableClass
```

如果你的目标是延迟，那么该收集器在多核机器上是一不错的选择。减少单个GC的停顿，直接影响应用终端用户体验，减少应用程序响应时间。由于大多时候，GC占用
CPU资源，而暂停执行应用。在CPU密集型应用中，CMS比并行GC有更差的吞吐量。

与之前其它GC算法相比，现在通过分析该算法日志（新生代和老年代GC）查看该算法的执行过程

```
2015-05-26T16:23:07.219-0200: 64.322: [GC (Allocation Failure) 64.322: [ParNew: 613404K->68068K(613440K), 0.1020465 secs] 10885349K->10880154K(12514816K), 0.1021309 secs] [Times: user=0.78 sys=0.01, real=0.11 secs]
2015-05-26T16:23:07.321-0200: 64.425: [GC (CMS Initial Mark) [1 CMS-initial-mark: 10812086K(11901376K)] 10887844K(12514816K), 0.0001997 secs] [Times: user=0.00 sys=0.00, real=0.00 secs]
2015-05-26T16:23:07.321-0200: 64.425: [CMS-concurrent-mark-start]
2015-05-26T16:23:07.357-0200: 64.460: [CMS-concurrent-mark: 0.035/0.035 secs] [Times: user=0.07 sys=0.00, real=0.03 secs]
2015-05-26T16:23:07.357-0200: 64.460: [CMS-concurrent-preclean-start]
2015-05-26T16:23:07.373-0200: 64.476: [CMS-concurrent-preclean: 0.016/0.016 secs] [Times: user=0.02 sys=0.00, real=0.02 secs]
2015-05-26T16:23:07.373-0200: 64.476: [CMS-concurrent-abortable-preclean-start]
2015-05-26T16:23:08.446-0200: 65.550: [CMS-concurrent-abortable-preclean: 0.167/1.074 secs] [Times: user=0.20 sys=0.00, real=1.07 secs]
2015-05-26T16:23:08.447-0200: 65.550: [GC (CMS Final Remark) [YG occupancy: 387920 K (613440 K)]65.550: [Rescan (parallel) , 0.0085125 secs]65.559: [weak refs processing, 0.0000243 secs]65.559: [class unloading, 0.0013120 secs]65.560: [scrub symbol table, 0.0008345 secs]65.561: [scrub string table, 0.0001759 secs][1 CMS-remark: 10812086K(11901376K)] 11200006K(12514816K), 0.0110730 secs] [Times: user=0.06 sys=0.00, real=0.01 secs]
2015-05-26T16:23:08.458-0200: 65.561: [CMS-concurrent-sweep-start]
2015-05-26T16:23:08.485-0200: 65.588: [CMS-concurrent-sweep: 0.027/0.027 secs] [Times: user=0.03 sys=0.00, real=0.03 secs]
2015-05-26T16:23:08.485-0200: 65.589: [CMS-concurrent-reset-start]
2015-05-26T16:23:08.497-0200: 65.601: [CMS-concurrent-reset: 0.012/0.012 secs] [Times: user=0.01 sys=0.00, real=0.01 secs]
```

## Minor GC

上面日志的第一个事件是新生代GC，让我们从上段日志中分析GC的行为。

<code>
  2015-05-26T16:23:07.219-0200<sup>1</sup> : 64.322<sup>2</sup>:[GC<sup>3</sup>(Allocation Failure<sup>4</sup>) 64.322: [ParNew<sup>5</sup>: 613404K->68068K<sup>6</sup>(613440K)<sup>7</sup>, 0.1020465 secs<sup>8</sup>] 10885349K->10880154K<sup>9</sup>(12514816K)<sup>10</sup>, 0.1021309 secs<sup>11</sup>][Times: user=0.78 sys=0.01, real=0.11 secs]<sup>12</sup>
</code>
<br>
<br>

1、2015-05-26T16:23:07.219-0200 - GC开始时间  
2、64.322 - GC开始时间，相对于JVM启动后的过去64.322秒，以秒为单位。  
3、GC - 区分FullGC和Minor GC，本次是MinorGC  
4、Allocation Failure - 收集原因，此场景下，因为在新生代中无法分配到请求的内存触发新生代GC。  
5、ParNew - 收集器名字，说明在新生代中采用并发标记-复制有Stop-the-world的收集器，设计它的目标是与老年代CMS收集器一起工作。  
6、613404K->68068K - 新生代在收集之前和之后的所占大小。  
7、613440K - 新生代总大小。  
8、0.1020465 - 清理完所消耗的时间。  
9、10885349K->10880154K - 收集前后总使用堆的大小。  
10、12514816K - 总的可用堆大小。  
11、0.1021309 - 整个垃圾收集时间。包括标记、复制新生代存活对象、对象升级到老年代。  
12、Times: user=0.78 sys=0.01, real=0.11 secs  
> * user：垃圾收集占用的CPU时间  
> * sys：调用或等待系统事件所占用的时间  
> * real：应用被占停的时时间。在并行GC中，该值接近于(user time + system time) / GC的线程数量   

从上面的日志中，我们可以分析出得，收集之前总使用堆是10885349K，新生代总大小613404K。说明老年代大小为10271945K。收集之后，新生代占用大小减少了
545336K，但是总堆大小只减少了5195K，说明有540141K(545336-5195)的对象从新代升级到了老年年。

![堆在GC前后的变化](ParallelGC-in-Young-Generation-Java.jpg)


## Full GC

到此，已能阅读Minor GC的日志文件，本节介绍完全不同的垃圾收集事件的日志文件。下文为老年代CMS GC各个不同阶段的日志文件，我们一一介绍，并了解日志内容，下面为事个CMS垃圾收集日志的完整内容。


```
2015-05-26T16:23:07.321-0200: 64.425: [GC (CMS Initial Mark) [1 CMS-initial-mark: 10812086K(11901376K)] 10887844K(12514816K), 0.0001997 secs] [Times: user=0.00 sys=0.00, real=0.00 secs]
2015-05-26T16:23:07.321-0200: 64.425: [CMS-concurrent-mark-start]
2015-05-26T16:23:07.357-0200: 64.460: [CMS-concurrent-mark: 0.035/0.035 secs] [Times: user=0.07 sys=0.00, real=0.03 secs]
2015-05-26T16:23:07.357-0200: 64.460: [CMS-concurrent-preclean-start]
2015-05-26T16:23:07.373-0200: 64.476: [CMS-concurrent-preclean: 0.016/0.016 secs] [Times: user=0.02 sys=0.00, real=0.02 secs]
2015-05-26T16:23:07.373-0200: 64.476: [CMS-concurrent-abortable-preclean-start]
2015-05-26T16:23:08.446-0200: 65.550: [CMS-concurrent-abortable-preclean: 0.167/1.074 secs] [Times: user=0.20 sys=0.00, real=1.07 secs]
2015-05-26T16:23:08.447-0200: 65.550: [GC (CMS Final Remark) [YG occupancy: 387920 K (613440 K)]65.550: [Rescan (parallel) , 0.0085125 secs]65.559: [weak refs processing, 0.0000243 secs]65.559: [class unloading, 0.0013120 secs]65.560: [scrub symbol table, 0.0008345 secs]65.561: [scrub string table, 0.0001759 secs][1 CMS-remark: 10812086K(11901376K)] 11200006K(12514816K), 0.0110730 secs] [Times: user=0.06 sys=0.00, real=0.01 secs]
2015-05-26T16:23:08.458-0200: 65.561: [CMS-concurrent-sweep-start]
2015-05-26T16:23:08.485-0200: 65.588: [CMS-concurrent-sweep: 0.027/0.027 secs] [Times: user=0.03 sys=0.00, real=0.03 secs]
2015-05-26T16:23:08.485-0200: 65.589: [CMS-concurrent-reset-start]
2015-05-26T16:23:08.497-0200: 65.601: [CMS-concurrent-reset: 0.012/0.012 secs] [Times: user=0.01 sys=0.00, real=0.01 secs]
```




特别说明：在真实的场景中，新生代的Minor GC可以在老年代并发收集期间的任何时间执行。此场景下的Minor GC记录在之前章节的Minor GC事件日志中类似。  
