jvm参数总共有几百个，没人能全部记下来，下面主要收集我们平时开发和面试常见的。     
这些参数主要基于jdk8，参考oracle官方文档：https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html    
-开头参数：表示标准参数，如-D,-javeagent。   
-X开头参数：表示非标准参数，不是所以虚拟机都支持它们，也可能会发生变化。   
-XX开头参数：表示非标准不稳定参数，与-X类似，不是所有虚拟机都支持，也可能会发生变化，相比-X更加不稳定。    

参数 | 解释
---|---
**内存**|
-Xss: | 指定线程栈大小，默认是1M  
-Xms: | 指定jvm初始堆大小  
-Xmx: | 指定jvm最大可用内存  
-Xmn：| 设置新生代内存大小，设置这个参数会覆盖NewRatio参数
-XX:NewRatio | 老年代:新生代，默认值是 2:1
-XX:SurvivorRatio | eden和survivor比值，默认是8，即eden:s0:s1=1:1:8
-XX:MetaspaceSize | 元数据空间初始大小
-XX:MaxMetaspaceSize | 元数据空间最大大小  
-XX:MaxDirectMemorySize | 最大堆外内存  
-XX:+HeapDumpOnOutOfMemoryError | 开启oom head dump  
-XX:HeapDumpPath|dump文件路径
**gc**|
-XX:MaxTenuringThreshold | 默认值是15，进入老年代的年龄。cms默认是6。
-XX:+DisableExplicitGC | 明确禁止使用system.gc  
-XX:+ParallelRefProcEnabled | 并行处理Reference对象，默认是false。
**gc log**|
-XX:+PrintGCDetails | 打印gc日志  
-XX:+PrintGCDateStamps | 打印gc时间  
-Xloggc | gc log日志路径   
-XX:UseGCLogFileRotation | 是否开启gc log文件滚动输出，前提是配置了-Xloggc
-XX:NumberOfGCLogFiles | gc log滚动输出文件个数，会生成filename.0,filename.1,filename.n-1文件
-XX:GCLogFileSize | gc log滚动大小，超过这个大小就会写入下一个文件，最小是8kb
**parallel gc**
-XX:+UseParallelGC/-XX:+UseParallelOldGC | 年轻代/老年代使用parallel gc，这两个参数开启一个会自动开启另一个，jdk8中默认true
-XX:ParallelGCThreads | gc并发收集线程数，默认和cpu核数相等
-XX:+UseAdaptiveSizePolicy | 开启自适应调节，不需要设置新生代eden，survivor比例，新生晋升老年代年龄，jvm会根据监控动态调整，默认true
-XX:MaxGCPauseMillis | 期望gc最大停顿时间，默认0，即不限制。只对parallel scavenge有效。
-XX:GCTimeRatio | 期望用户代码运行时间占总时间比率，默认99，即gc时间控制在1%
**cms gc**
-XX:+UseConcMarkSweepGC | 开启cms收集器，默认是不开启。新生代会自动开启ParNew，老年代备用Serial Old。
-XX:+UseCMSInitiatingOccupancyOnly | 使用设定的阈值开始cms 
-XX:CMSInitiatingOccupancyFraction | 达到这个百分比即开始cms gc（cms是并发清理，需预留内存），默认是92%，UseCMSInitiatingOccupancyOnly开启这个参数才生效
-XX:CMSScavengeBeforeRemark | cms重新标记阶段是否先进行一次minor gc,减少gc root扫描，默认值是false
-XX:+UseCMSCompactAtFullCollection | full gc后对内存进行整理，默认值是true。
-XX:CMSFullGCsBeforeCompaction | 与UseCMSCompactAtFullCollection搭配使用，多少次full gc后才对老年代进行整理。jdk8默认值是0，表示每次full gc后对内存进行整理。
-XX:ParallelCMSThreads | 设置cms并发线程数，默认是（cpu核数+3）/ 4
**g1 gc**
-XX:+UseG1GC | 使用g1收集器，jdk9开始默认使用g1收集器       
-XX:MaxGCPauseMills | gc最大停顿时间，默认是200ms   
-XX:G1HeapRegionSize | g1 region的个数，1~32m之间，2的整数倍，默认根据堆大小自动计算   
-XX:InitiatingHeapOccupancyPercent | 老年代达到这个阈值就开始mixed gc，默认45%   
**TLAB**   
-XX:+UseTLAB | 开启TLAB机制，默认是开启
-XX:TLABWasteTargetPercent | TLAB占用eden区大小，默认是1%
-XX:TLABSize | 设置TLAB大小，默认是0，jvm自动调节
XX:+ResizeTLAB | 开启TLAB自动调节，默认是开启
-XX:TLABRefillWasteFraction | TLAB最大浪费空间，默认是64，表示TLAB大小的1/64   
-XX+PringTLAB | gc日志打印TLAB信息，默认是不打印
**其它**|  
-XX:ErrorFile|jvm crash 路径   
-XX:+DoEscapeAnalysis | 开启逃逸分析，默认就是开启。
-XX:+EliminateAllocations | 开启标量替换，默认就是开启。
-XX:+UseCompressedOops | 开启指针压缩，默认就是开启。
**gc工具**|
[perfma](https://opts.console.perfma.com/) | 参数查询，解释，优化  
[fastthread](https://fastthread.io),[gceasy](https://gceasy.io/)|在线，文件大了上传很慢
jvisualvm+visualGC插件|
mat|
