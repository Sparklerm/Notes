## JVM参数

### 堆的限制

JVM中最大堆大小有三方面限制:

- 相关操作系统的数据模型（32-bt还是64-bit）限制
- 系统的可用虚拟内存限制
- 系统的可用物理内存限制

32位系统下，一般限制在1.5G~2G；64为操作系统对内存无限制

### 堆参数

- -Xmx: 指定JVM的最大堆大小，如:-Xmx=2g
- -Xms: 指定JVM的最小堆大小，如:-Xms=2g，高并发应用，建议和-Xmx一样，防止因为内存收缩／突然增大带来的性能影响。
- -Xmn: 指定JVM中NewGeneration的大小，如:-Xmn256m。这个参数很影响性能，如果程序需要比较多的临时内存，可以适当设置高点。
- -XX:PermSize: 指定JVM中PermGeneration的最小值，如:-XX:PermSize=32m，在Java8中此参数被忽略，永久代由元空间代替。
- -XX:MaxPermSize: 指定JVM中PermGeneration的最大值，如:-XX:MaxPermSize=64m，在Java8中此参数被忽略，永久代由元空间代替。
- -Xss: 指定线程桟大小，如:-Xss128k，一般来说，WEB框架下的应用需要256K，如果程序有大规模的递归行为，可以设置到512K／1M。这个需要全面的测试才能知道。不过，256K已经很大了。这个参数对性能的影响比较大的。
- -XX:NewRatio: 指定JVM中OldGeneration堆与NewGeneration的比例（OldSize/NewSize），在使用CMSGC(Concurrent Mark-Sweep GC)的情况下此参数失效，如:-XX:NewRatio=2
- -XX:SurvivorRatio: 指定NewGeneration中EdenSpace与一个SurvivorSpace的堆size比例，-XX:SurvivorRatio=8，那么在总共NewGeneration为10m的情况下，EdenSpace为8m
- -XX:MinHeapFreeRatio: 指定JVMheap在使用率小于n的情况下，heap进行收缩，Xmx==Xms的情况下无效，如:-XX:MinHeapFreeRatio=30
- -XX:MaxHeapFreeRatio: 指定JVMheap在使用率大于n的情况下，heap进行扩张，Xmx==Xms的情况下无效，如:-XX:MaxHeapFreeRatio=70
- -XX:LargePageSizeInBytes: 指定Java堆的分页页面大小，如:-XX:LargePageSizeInBytes=128m

### 元空间

持久代的空间被彻底地删除了，它被一个叫元空间的区域所替代了。持久代删除了之后，很明显，JVM会忽略PermSize和MaxPermSize这两个参数，还有就是你再也看不到java.lang.OutOfMemoryError: PermGen error的异常了。

JDK 8的HotSpot JVM现在使用的是本地内存来表示类的元数据，这个区域就叫做元空间,绝大多数的类元数据的空间都从本地内存中分配。

### 垃圾收集器参数

- -XX:+UseParallelGC: 指定在NewGeneration使用ParallelCollector，并行收集，暂停APPThreads，同时启动多个垃圾回收Thread，不能和CMSGC一起使用.系统吨吐量优先，但是会有较长时间的AppPause，后台系统任务可以使用此GC。
- -XX:ParallelGCThreads: 指定parallelcollection时启动的Thread个数，默认是物理Processor的个数，
- -XX:+UseParallelOldGC: 指定在OldGeneration使用ParallelCollector
- -XX:+UseParNewGC: 指定在NewGeneration使用ParallelCollector，是UseParallelGC的gc的升级版本，有更好的性能或者优点，可以和CMSGC一起使用
- -XX:+CMSParallelRemarkEnabled: 在使用UseParNewGC的情况下，尽量减少mark的时间
- -XX:+UseConcMarkSweepGC: 指定在OldGeneration使用CMSGC，GCThread和APPThread并行(在init-mark和remark时PauseAPPThread)。apppause时间较短，适合交互性强的系统，如webserver
- -XX:+UseCMSCompactAtFullCollection: 在使用ConcurrentGC的情况下，防止MemoryFragmention，对LiveObject进行整理，使Memory碎片减少
- -XX:CMSFullGCsBeforeCompaction: 上面配置开启的情况下，这里设置多少次FullGC后，对年老代进行压缩
- -XX:CMSInitiatingOccupancyFraction: 指示在oldgeneration在使用了n%的比例后，启动ConcurrentCollector，默认值是68，如:-XX:CMSInitiatingOccupancyFraction=70 有个bug，在低版本(1.5.09andearly)的JVM上出现，http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=6486089
- -XX:+UseCMSInitiatingOccupancyOnly 指示只有在OldGeneration在使用了初始化的比例后ConcurrentCollector启动收集

## 其他

- -XX:MaxDirectMemorySize: 指定本地直接内存大小，如果不指定，则默认与Java堆的最大值-Xmx指定一样。
- -XX:MaxTenuringThreshold: 指定一个Object在经历了n次YoungGC后转移到OldGeneration区，在Linux64的Java6下默认值是15，此参数对于throughputcollector无效，如:-XX:MaxTenuringThreshold=31
- -XX:+DisableExplicitGC: 禁止Java程序中的FullgGC，如System.gc()的调用.最好加上，防止程序在代码里误用了。对性能造成冲击。
- -XX:+UseFastAccessorMethods: get，set方法转成本地代码
- -XX:+PrintGCDetails: 打应垃圾收集的情况如:[GC15610.466:[ParNew:229689K->20221K(235968K)，0.0194460secs]1159829K->953935K(2070976K)，0.0196420secs]
- -XX:+PrintGCTimeStamps: 打应垃圾收集的时间情况，如:[Times:user=0.09,sys=0.00，real=0.02secs]
- -XX:+PrintGCApplicationStoppedTime: 打应垃圾收集时，系统的停顿时间，如:Total-Time-For-Which-Application-Threads-Were-Stopped:0.0225920seconds