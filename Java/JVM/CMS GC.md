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

2015-05-26T16:23:07.219-0200\ :sup:`1` : 64.3222:[GC3(Allocation Failure4) 64.322: [ParNew5: 613404K->68068K6(613440K) 7, 0.1020465 secs8] 10885349K->10880154K 9(12514816K)10, 0.1021309 secs11][Times: user=0.78 sys=0.01, real=0.11 secs]12