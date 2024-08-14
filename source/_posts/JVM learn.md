---
title: JVM Learn
date: 2021-09-01 08:35:47
tags: learn
categories: java
---

## 堆内与堆外

### 堆

- 常说的堆，指的是堆内内存，由JVM「托管」的内存区域。对应的英文是：on-heap Memory。所有的对象都是放在堆中。

- 堆的特点：

- - 由GC管理，一旦堆大小大于1GB会出现比较明显的暂停。
  - 对象存储到GC中无需进行序列化、反序列化的。

### 堆外

- 堆外就是JVM开辟出来的，堆以外的一块「非托管」内存区域。对应的英文是：off-heap Memory。堆外内存也是 JVM 进程开辟的一块内存空间。只不过，GC不会「光顾」这里。

- 堆外的特点：

- - 由操作系统直接管理。别的进程能够直接访问。
  - 特别适合分配次数少，读写非常频繁的场景。
  - 可以避免在分配GB级别的内存而造成GC开销。

- 堆外的缺点：

- - 容易出现内存泄漏，难以排查。
  - 对象的存储需要序列化、和反序列化。

因为堆外内存由此特点，很多高性能的应用组件基于off-heap来提高性能。

#### 堆外内存常配合使用System GC

> 堆外内存主要针对java.nio.DirectByteBuffer，这些对象的创建过程会通过Unsafe接口直接通过os::malloc来分配内存，然后将内存的起始地址和大小存到java.nio.DirectByteBuffer对象里，这样就可以直接操作这些内存。这些内存只有在DirectByteBuffer回收掉之后才有机会被回收，因此如果这些对象大部分都移到了old，但是一直没有触发CMS GC或者Full GC，那么悲剧将会发生，因为你的物理内存被他们耗尽了，因此为了避免这种悲剧的发生，通过-XX:MaxDirectMemorySize来指定最大的堆外内存大小，当使用达到了阈值的时候将调用System.gc来做一次full gc，以此来回收掉没有被使用的堆外内存。

### System.gc常识

- system.gc其实是做一次full gc
- system.gc会暂停整个进程
- system.gc一般情况下我们要禁掉，使用-XX:+DisableExplicitGC
- system.gc在cms gc下我们通过-XX:+ExplicitGCInvokesConcurrent来做一次稍微高效点的GC(效果比Full GC要好些)
- system.gc最常见的场景是RMI/NIO下的堆外内存分配等

# jvm常用参数的设置和含义

**1.设置堆的最大和最小值**
 -Xmx20M(最大值) ,-Xms20M(最小值)。

```
-Xms和-Xmx实际上是 -XX:InitialHeapSize 和-XX:MaxHeapSize 的缩写
```

**2.设置栈的大小**
 -Xss128k： 设置每个线程的堆栈大小。JDK5.0以后每个线程堆栈大小为1M，以前每个线程堆栈大小为256K。更具应用的线程所需内存大小进行调整。在相同物理内 存下，减小这个值能生成更多的线程。但是操作系统对一个进程内的线程数还是有限制的，不能无限生成，经验值在3000~5000左右。

功能开关:

| 参数                           | 默认值或限制                                            | 说明                                                                                                          |
| ---------------------------- | ------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| -XX:-AllowUserSignalHandlers | 限于Linux和Solaris，默认不启用                             | 允许为java进程安装信号处理器,信号处理参见类:sun.misc.Signal, sun.misc.SignalHandler                                            |
| -XX:+DisableExplicitGC       | 默认启用                                              | 禁止在运行期显式地调用System.gc()                                                                                      |
| -XX:+FailOverToOldVerifier   | Java6新引入选项，默认启用                                   | 如果新的Class校验器检查失败，则使用老的校验器(失败原因:因为JDK6最高向下兼容到JDK1.2，而JDK1.2的class info 与JDK6的info存在较大的差异，所以新校验器可能会出现校验失败的情况) |
| -XX:+HandlePromotionFailure  | java5以前是默认不启用，java6默认启用                           | 关闭新生代收集担保                                                                                                   |
| -XX:+MaxFDLimit              | 限于Solaris，默认启用                                    | 设置java进程可用文件描述符为操作系统允许的最大值。                                                                                 |
| -XX:PreBlockSpin=10          | -XX:+UseSpinning 必须先启用，对于java6来说已经默认启用了，这里默认自旋10次 | 控制多线程自旋锁优化的自旋次数                                                                                             |
| -XX:-RelaxAccessControlCheck | 默认不启用                                             | 在Class校验器中，放松对访问控制的检查,作用与reflection里的setAccessible类似                                                        |
| -XX:+ScavengeBeforeFullGC    | 默认启用                                              | 在Full GC前触发一次Minor GC                                                                                       |
| -XX:+UseAltSigs              | 限于Solaris，默认启用                                    | 为了防止与其他发送信号的应用程序冲突，允许使用候补信号替代 SIGUSR1和SIGUSR2                                                               |
| -XX:+UseBoundThreads         | 限于Solaris, 默认启用                                   | 绑定所有的用户线程到内核线程, 减少线程进入饥饿状态（得不到任何cpu time）的次数                                                                |
| -XX:-UseConcMarkSweepGC      | 默认不启用                                             | 启用CMS低停顿垃圾收集器,减少FGC的暂停时间                                                                                    |
| -XX:+UseGCOverheadLimit      | 默认启用                                              | 限制GC的运行时间。如果GC耗时过长，就抛OOM                                                                                    |
| -XX:+UseLWPSynchronization   | 限于solaris，默认启用                                    | 使用轻量级进程（内核线程）替换线程同步                                                                                         |
| -XX:-UseParallelGC           | -server时启用,其他情况下，默认不启用                            | 策略为新生代使用并行清除，年老代使用单线程Mark-Sweep-Compact的垃圾收集器                                                               |
| -XX:-UseParallelOldGC        | 默认不启用                                             | 策略为老年代和新生代都使用并行清除的垃圾收集器                                                                                     |
| -XX:-UseSerialGC             | -client时启用,其他情况下，默认不启用                            | 使用串行垃圾收集器                                                                                                   |
| -XX:-UseSpinning             | java1.4.2和1.5需要手动启用, java6默认已启用                   | 启用多线程自旋锁优化                                                                                                  |
| -XX:+UseTLAB                 | 1.4.2以前和使用-client选项时，默认不启用，其余版本默认启用               | 启用线程本地缓存区                                                                                                   |
| -XX:+UseSplitVerifier        | java5默认不启用, java6默认启用                             | 使用新的Class类型校验器                                                                                              |
| -XX:+UseThreadPriorities     | 默认启用                                              | 使用本地线程的优先级                                                                                                  |
| -XX:+UseVMInterruptibleIO    | 限于solaris，默认启用                                    | 在solaris中，允许运行时中断线程                                                                                         |

性能参数:

| 参数                            | 默认值或限制                                                                                                | 说明                                                                                                                                                                                                                                                |
| ----------------------------- | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -XX:+AggressiveOpts           | JDK 5 update 6后引入，但需要手动启用, JDK6默认启用                                                                   | 启用JVM开发团队最新的调优成果。例如编译优化，偏向锁，并行年老代收集等                                                                                                                                                                                                              |
| -XX:CompileThreshold=10000    | 1000                                                                                                  | 通过JIT编译器，将方法编译成机器码的触发阀值，可以理解为调用方法的次数，例如调1000次，将方法编译为机器码                                                                                                                                                                                           |
| -XX:LargePageSizeInBytes=4m   | 默认4m, amd64位：2m                                                                                       | 设置堆内存的内存页大小                                                                                                                                                                                                                                       |
| -XX:MaxHeapFreeRatio=70       | 70                                                                                                    | GC后，如果发现空闲堆内存占到整个预估上限值的70%，则收缩预估上限值                                                                                                                                                                                                               |
| -XX:MaxNewSize=size           | 1.3.1 Sparc: 32m, 1.3.1 x86: 2.5m                                                                     | 新生代占整个堆内存的最大值                                                                                                                                                                                                                                     |
| -XX:MaxPermSize=64m           | 5.0以后: 64 bit VMs会增大预设值的30%, 1.4 amd64: 96m, 1.3.1 -client: 32m, 其他默认 64m                             | Perm（俗称方法区）占整个堆内存的最大值                                                                                                                                                                                                                             |
| -XX:MinHeapFreeRatio=40       | 40                                                                                                    | GC后，如果发现空闲堆内存占到整个预估上限值的40%，则增大上限值                                                                                                                                                                                                                 |
| -XX:NewRatio=2                | Sparc -client: 8, x86 -server: 8, x86 -client: 12, -client: 4 (1.3), 8 (1.3.1+), x86: 12, 其他默认 2      | 新生代和年老代的堆内存占用比例, 例如2表示新生代占年老代的1/2，占整个堆内存的1/3                                                                                                                                                                                                      |
| -XX:NewSize=2.125m            | 5.0以后: 64 bit Vms 会增大预设值的30%, x86: 1m, x86, 5.0以后: 640k, 其他默认 2.125m                                  | 新生代预估上限的默认值                                                                                                                                                                                                                                       |
| -XX:ReservedCodeCacheSize=32m | Solaris 64-bit, amd64, -server x86: 48m, 1.5.0_06之前, Solaris 64-bit amd64: 1024m, 其他默认 32m            | 设置代码缓存的最大值，编译时用                                                                                                                                                                                                                                   |
| -XX:SurvivorRatio=8           | Solaris amd64: 6, Sparc in 1.3.1: 25, Solaris platforms 5.0以前: 32, 其他默认 8                             | Eden与Survivor的占用比例。例如8表示，一个survivor区占用 1/8 的Eden内存，即1/10的新生代内存，为什么不是1/9？ 因为我们的新生代有2个survivor，即S0和S1。所以survivor总共是占用新生代内存的 2/10，Eden与新生代的占比则为 8/10                                                                                                 |
| -XX:TargetSurvivorRatio=50    | 50                                                                                                    | 实际使用的survivor空间大小占比。默认是50%，最高90%                                                                                                                                                                                                                  |
| -XX:ThreadStackSize=512       | Sparc: 512, Solaris x86: 320 (5.0以前 256), Sparc 64 bit: 1024, Linux amd64: 1024 (5.0 以前 0), 其他默认 512. | 线程堆栈大小                                                                                                                                                                                                                                            |
| -XX:+UseBiasedLocking         | JDK 5 update 6后引入，但需要手动启用, JDK6默认启用                                                                   | 启用偏向锁                                                                                                                                                                                                                                             |
| -XX:+UseFastAccessorMethods   | 默认启用                                                                                                  | 优化原始类型的getter方法性能(get/set:Primitive Type)                                                                                                                                                                                                         |
| -XX:-UseISM                   | 默认启用                                                                                                  | 启用solaris的ISM                                                                                                                                                                                                                                     |
| -XX:+UseLargePages            | JDK 5 update 5后引入，但需要手动启用, JDK6默认启用                                                                   | 启用大内存分页                                                                                                                                                                                                                                           |
| -XX:+UseMPSS                  | 1.4.1 之前: 不启用, 其余版本默认启用                                                                               | 启用solaris的MPSS，不能与ISM同时使用                                                                                                                                                                                                                         |
| -XX:+UseStringCache           | 默认开启                                                                                                  | 启用缓存常用的字符串。                                                                                                                                                                                                                                       |
| -XX:AllocatePrefetchLines=1   | 1                                                                                                     | Number of cache lines to load after the last object allocation using prefetch instructions generated in JIT compiled code. Default values are 1 if the last allocated object was an instance and 3 if it was an array.                            |
| -XX:AllocatePrefetchStyle=1   | 1                                                                                                     | Generated code style for prefetch instructions. 0 – no prefetch instructions are generate*d*, 1 – execute prefetch instructions after each allocation, 2 – use TLAB allocation watermark pointer to gate when prefetch instructions are executed. |
| -XX:+UseCompressedStrings     | Java 6 update 21有一选项                                                                                  | 其中，对于不需要16位字符的字符串，可以使用byte[] 而非char[]。对于许多应用，这可以节省内存，但速度较慢（5％-10％）                                                                                                                                                                                |
| -XX:+OptimizeStringConcat     | 在Java 6更新20中引入                                                                                        | 优化字符串连接操作在可能的情况下                                                                                                                                                                                                                                  |

调试参数:

| 参数                                              | 默认值或限制                        | 说明                                                                                                                                                                                                                                                                                                                                      |
| ----------------------------------------------- | ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -XX:-CITime                                     |                               | 打印发费在JIT编译上的时间                                                                                                                                                                                                                                                                                                                          |
| -XX:ErrorFile=./hs_err_pid<pid>.log             | JDK6中引入                       | 错误文件                                                                                                                                                                                                                                                                                                                                    |
| -XX:-ExtendedDTraceProbes                       | JDK6中引入仅在Solaris              | 启用性能的影响DTrace探测器                                                                                                                                                                                                                                                                                                                        |
| -XX:HeapDumpPath=./java_pid<pid>.hprof          | 1.4.2 update 12, 5.0 update 7 | 指定HeapDump的文件路径或目录                                                                                                                                                                                                                                                                                                                      |
| -XX:-HeapDumpOnOutOfMemoryError                 | 1.4.2 update 12, 5.0 update 7 | 当抛出OOM时进行HeapDump                                                                                                                                                                                                                                                                                                                       |
| -XX:OnError=”<cmd args>;<cmd args>”             | 1.4.2 update 9                | 当发生错误时执行用户指定的命令                                                                                                                                                                                                                                                                                                                         |
| -XX:OnOutOfMemoryError=”<cmd args>; <cmd args>” | 1.4.2 update 12, 6            | 当发生OOM时执行用户指定的命令                                                                                                                                                                                                                                                                                                                        |
| -XX:-PrintClassHistogram                        | 1.4.2                         | 当Ctrl+Break发生时打印Class实例信息,与jmap -histo相同                                                                                                                                                                                                                                                                                                |
| -XX:-PrintConcurrentLocks                       | 6                             | 当Ctrl+Break发生时打印java.util.concurrent的锁信息, 与jstack -l相同                                                                                                                                                                                                                                                                                  |
| -XX:-PrintCommandLineFlags                      | 5                             | 打印命令行上的标记                                                                                                                                                                                                                                                                                                                               |
| -XX:-PrintCompilation                           |                               | 当方法被编译时打印信息                                                                                                                                                                                                                                                                                                                             |
| -XX:-PrintGC                                    |                               | 当GC发生时打印信息                                                                                                                                                                                                                                                                                                                              |
| -XX:-PrintGCDetails                             | 1.4.0                         | 打印GC详细信息                                                                                                                                                                                                                                                                                                                                |
| -XX:-PrintGCTimeStamps                          | 1.4.0                         | 打印GC用时                                                                                                                                                                                                                                                                                                                                  |
| -XX:-PrintTenuringDistribution                  |                               | 打印Tenuring年龄信息                                                                                                                                                                                                                                                                                                                          |
| -XX:-TraceClassLoading                          |                               | 跟踪类加载                                                                                                                                                                                                                                                                                                                                   |
| -XX:-TraceClassLoadingPreorder                  | 1.4.2                         | 跟踪所有加载的引用类                                                                                                                                                                                                                                                                                                                              |
| -XX:-TraceClassResolution                       | 1.4.2                         | 跟踪常量池的变化                                                                                                                                                                                                                                                                                                                                |
| -XX:-TraceClassUnloading                        |                               | 跟踪类的卸载                                                                                                                                                                                                                                                                                                                                  |
| -XX:-TraceLoaderConstraints                     | 6                             | Trace recording of loader constraints                                                                                                                                                                                                                                                                                                   |
| -XX:+PerfSaveDataToFile                         |                               | 退出时保存jvmstat二进制文件                                                                                                                                                                                                                                                                                                                       |
| -XX:ParallelGCThreads=                          |                               | 设置新生代与老年代并行垃圾回收器的线程数                                                                                                                                                                                                                                                                                                                    |
| -XX:+UseCompressedOops                          |                               | Enables the use of compressed pointers (object references represented as 32 bit offsets instead of 64-bit pointers) for optimized 64-bit performance with Java heap sizes less than 32gb.                                                                                                                                               |
| -XX:+AlwaysPreTouch                             |                               | Pre-touch the Java heap during JVM initialization. Every page of the heap is thus demand-zeroed during initialization rather than incrementally during application execution.                                                                                                                                                           |
| -XX:AllocatePrefetchDistance=                   |                               | Sets the prefetch distance for object allocation. Memory about to be written with the value of new objects is prefetched into cache at this distance (in bytes) beyond the address of the last allocated object. Each Java thread has its own allocation point. The default value varies with the platform on which the JVM is running. |
| -XX:InlineSmallCode=                            |                               | 当编译的代码小于指定的值时,内联编译的代码                                                                                                                                                                                                                                                                                                                   |
| -XX:MaxInlineSize=35                            |                               | 内联方法的最大字节数                                                                                                                                                                                                                                                                                                                              |
| -XX:FreqInlineSize=                             |                               | 内联频繁执行的方法的最大字节码大小                                                                                                                                                                                                                                                                                                                       |
| -XX:LoopUnrollLimit=                            |                               | Unroll loop bodies with server compiler intermediate representation node count less than this value. The limit used by the server compiler is a function of this value, not the actual value. The default value varies with the platform on which the JVM is running.                                                                   |
| -XX:InitialTenuringThreshold=7                  |                               | 设置初始的对象在新生代中最大存活次数                                                                                                                                                                                                                                                                                                                      |
| -XX:MaxTenuringThreshold=                       |                               | 设置对象在新生代中最大的存活次数,最大值15,并行回收机制默认为15,CMS默认为4                                                                                                                                                                                                                                                                                              |



# Garbage Collectors

The JVM (Java Virtual Machine) has several different garbage collectors, including the **Parallel Scavenge collector** and the **G1 (Garbage-First) collector**. Both collectors have a "young generation" that is responsible for storing newly created objects, but they work in different ways.

- Parallel Scavenge collector

The Parallel Scavenge collector's young generation, also known as the "PS young generation", is divided into two regions called "Eden" and "Survivor". The Eden region is where newly created objects are initially allocated, while the Survivor regions are used to store objects that have survived one or more garbage collection cycles. The idea is to quickly identify and collect objects that are no longer needed, while promoting long-lived objects to the older generation.

- G1 collector

The G1 collector also has a young generation, which is referred to as the "G1 young generation". Unlike the PS young generation, the G1 young generation does not have fixed regions like Eden and Survivor. Instead, the G1 collector divides the heap into equal-sized "regions" that can be used for both young and old objects, depending on their age and usage patterns. The G1 collector also uses a "remembered set" to keep track of references between regions, which allows it to more efficiently identify and collect objects that are no longer needed.

Overall, both the PS and G1 collectors have their own strengths and weaknesses, and the best choice depends on the specific requirements and characteristics of the application. The G1 collector is generally considered to be more flexible and adaptive to different usage patterns, but the PS collector may be better suited for applications with short-lived objects and high object creation rates.

# classLoader

## class冲突

可以打印出类的加载顺序，可以用来排查 class 的冲突问题：

```java
-XX:+TraceClassLoading
```



