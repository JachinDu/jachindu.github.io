## JVM调优

<font color='#02C874' size=5>**常用参数：https://www.cnblogs.com/anyehome/p/9071619.html**</font>

------



## JVM参数示例

配置： -Xmx4g –Xms4g –Xmn1200m –Xss512k -XX:NewRatio=4 -XX:SurvivorRatio=8 -XX:PermSize=100m

-XX:MaxPermSize=256m -XX:MaxTenuringThreshold=15

解析：
-Xmx4g：堆内存最大值为4GB。
-Xms4g：初始化堆内存大小为4GB 。
-Xmn1200m：设置年轻代大小为1200MB。增大年轻代后，将会减小年老代大小。此值对系统性能影响较大，Sun官方推荐配置为整个堆的3/8。
-Xss512k：设置每个线程的堆栈大小。JDK5.0以后每个线程堆栈大小为1MB，以前每个线程堆栈大小为256K。应根据应用线程所需内存大小进行调整。在相同物理内存下，减小这个值能生成更多的线程。但是操作系统对一个进程内的线程数还是有限制的，不能无限生成，经验值在3000~5000左右。
-XX:NewRatio=4：设置年轻代（包括Eden和两个Survivor区）与年老代的比值（除去持久代）。设置为4，则年轻代与年老代所占比值为1：4，年轻代占整个堆栈的1/5
-XX:SurvivorRatio=8：设置年轻代中Eden区与Survivor区的大小比值。设置为8，则两个Survivor区与一个Eden区的比值为2:8，一个Survivor区占整个年轻代的1/10
-XX:PermSize=100m：初始化永久代大小为100MB。
-XX:MaxPermSize=256m：设置持久代大小为256MB。

-XX:MaxTenuringThreshold=15：设置垃圾最大年龄。如果设置为0的话，则年轻代对象不经过Survivor区，直接进入年老代。对于年老代比较多的应用，可以提高效率。如果将此值设置为一个较大值，则年轻代对象会在Survivor区进行多次复制，这样可以增加对象再年轻代的存活时间，增加在年轻代即被回收的概论。

------

## ***JVM调优目标***

### \1. 何时需要做jvm调优？

   \1. heap 内存（老年代）持续上涨达到设置的最大内存值；
   \2. ==**Full GC 次数频繁；**==
   \3. ==**GC 停顿时间过长（超过1秒）；**==
   \4. ==应用出现OutOfMemory 等内存异常；==
   \5. 应用中有使用本地缓存且占用大量内存空间；

   \6. 系统吞吐量与响应性能不高或下降。

### \2. JVM调优原则

   1.多数的Java应用不需要在服务器上进行JVM优化；

   2.多数导致GC问题的Java应用，都不是因为我们参数设置错误，而是代码问题；

   3.在应用上线之前，先考虑将机器的JVM参数设置到最优（最适合）；

   4.减少创建对象的数量；

   5.减少使用全局变量和大对象；

   6.JVM优化是到最后不得已才采用的手段；

   7.在实际使用中，分析GC情况优化代码比优化JVM参数更好；

### \3. JVM调优目标

   \1. ==GC低停顿；==

   \2. ==GC低频率；==

   \3. ==低内存占用； ==

   \4. ==高吞吐量;==

### **JVM调优量化目标（示例）：**

   \1. Heap 内存使用率 <= 70%;

   \2. Old generation内存使用率<= 70%;

   \3. avgpause <= 1秒; 

   \4. Full gc 次数0 或 avg pause interval >= 24小时 ;

   注意：不同应用，其JVM调优量化目标是不一样的。

## ***JVM调优经验\**

\1. JVM调优经验总结

**JVM调优的一般步骤为：**

   第1步：分析GC日志及dump文件，判断是否需要优化，确定瓶颈问题点；

   第2步：确定JVM调优量化目标；

   第3步：确定JVM调优参数（根据历史JVM参数来调整）；

   第4步：调优一台服务器，对比观察调优前后的差异；

   第5步：不断的分析和调整，直到找到合适的JVM参数配置；

   第6步：找到最合适的参数，将这些参数应用到所有服务器，并进行后续跟踪。