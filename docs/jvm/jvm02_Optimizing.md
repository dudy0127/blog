# Jvm基本命令和模拟内存泄漏问题查找

[TOC]

## Jvm 参数类型

标准参数: 不会改变的参数 -help -version

x参数: 非标准化参数 -Xint(解释执行)  -Xcomp(第一次使用就编译成本地代码)  -Xmixed:混合模式

xx参数:非标转化参数,相对不稳定,主要用于JVM调优和Debug

xx参数分类: 

1. Boolean类型:    -XX:[+-]<name> 表示启用和禁用 name 属性

   -XX:+UserG1GC  

2. 属性值

   -Xms :  -XX:InitialHeapSize  

   -Xmx :  -XX:MaxHeapSize

     -Xms512m  -Xmx768m   

   -xss:  -XX:ThreadStackSize 

## jinfo 查看当前应用设置的参数值

查看全部参数

jinfo -flags <pid>  

```bash
$ jinfo -flags 45213
Attaching to process ID 45213, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.122-b04
Non-default VM flags: -XX:CICompilerCount=4 -XX:InitialHeapSize=268435456 -XX:MaxHeapSize=4294967296 -XX:MaxNewSize=1431306240 -XX:MinHeapDeltaBytes=524288 -XX:NewSize=89128960 -XX:OldSize=179306496 -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseParallelGC
Command line:
```

查看单个参数:

jinfo -flag  ThreadStackSize  <pid>

```bash
$ jinfo -flag ThreadStackSize 45531
-XX:ThreadStackSize=1024

$ jinfo -flag +PrintGC  45531 #jinfo 可以动态改变值 打印GC日志?可以改变 ThreadStackSize 吗？

```

## jstat 查看JVM统计信息

类装载: -class

```bash
$ jstat -class  45531               
Loaded  Bytes  Unloaded  Bytes     Time   
 15644 28719.1        0     0.0       9.06
```

JIT编译: -complier

```bash
$ jstat -compiler   45531 
Compiled Failed Invalid   Time   FailedType FailedMethod
   13096      1       0    64.05          1 org/springframework/util/ReflectionUtils doWithMethods

```

垃圾回收: -gc,-printcompilation

```bash
$ jstat -gc   45531       
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT   
43008.0 47104.0 672.0   0.0   1208320.0 289684.3  423936.0   151923.9  89048.0 85362.0 10968.0 10251.6     30    0.366   4      0.618    0.985
```



C: 总量   U:已用多少       CCS 是压缩类空间 在 Metaspace区

## jmap 导出内存快照

-XX:+HeapDumpOnOutOfMemoryError    自动导出

-XX:HeapDumpPath = ./

另外一种就是

 jmap -dump:format=b,file=heap.hprof  <pid>

```bash
$ jmap -dump:format=b,file=heap.hprof 47685
Dumping heap to /Users/dudy/IdeaProjects/dudy/study/jvm/heap.hprof ...
Heap dump file created
```



当然jmap 不只是 这些功能:

https://www.cnblogs.com/myna/p/7573843.html

dump 文件分析工具: Eclipse Memory Analyzer  

./MemoryAnalyzer -data ./workspace







https://docs.oracle.com/javase/8/docs/technotes/tools/unix/toc.html

http://xxfox.perfma.com/

https://ifeve.com/useful-jvm-flags-part-1-jvm-types-and-compiler-modes-2/