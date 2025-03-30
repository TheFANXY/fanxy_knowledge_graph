# 1. jvm堆一直在飙升 还不是内存泄露 排查解决

1. jvm堆一直在飙升 还不是内存泄露
2. jdk8 udp存在一个thread.interrupt 的 Bug
3. 偶发单机耗时突增
4. 单机持续耗时⾼
5. 服务运⾏⼏天就挂

**这个禁止抄袭！bd大佬的分享！如果想支持可以去了解路总监，这是陆总群里很猛的扫地僧大佬bd分享的。**

这是当时的堆监控 流量上来之后 明显的一直往上飙 看锯齿 还能给回收 京东内部的监控系统

![1](D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\8 面试场景题\2【jvm调优】jvm堆一直在飙升 还不是内存泄露.assets\2-1.png)

![2](D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\8 面试场景题\2【jvm调优】jvm堆一直在飙升 还不是内存泄露.assets\2-2.png)

一直在yong gc 没full gc yong gc看那样还有耗时增长的

yong gc后内存还升，流量小的时候 时快时慢 看起来还回收不了多少的意思【不是回收不了 是回收了 又创建了】 那系统没有啥热点

![2](D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\8 面试场景题\2【jvm调优】jvm堆一直在飙升 还不是内存泄露.assets\2-3.png)

流量大的时候就是第一个糊了的图 怀疑是内存泄露 dump了3g多

因为dump大 mat打开费尽 所以用了个BHeapSampler 这个工具 不过效果不好 就不给你们发图了 知道有这玩意就行 jprofile也不错 但是收费

![4](D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\8 面试场景题\2【jvm调优】jvm堆一直在飙升 还不是内存泄露.assets\2-4.png)

当时的疑似内存泄露是显示成这样的  显然这点加起来都不算啥泄露 这里mat给的图是疑似 不代表一定

但是你要是看上g了就可能真的是泄露  这是2个疑似泄露的点 点进去看 就这样

![5](D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\8 面试场景题\2【jvm调优】jvm堆一直在飙升 还不是内存泄露.assets\2-5.png)

![6](D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\8 面试场景题\2【jvm调优】jvm堆一直在飙升 还不是内存泄露.assets\2-6.png)

![7](D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\8 面试场景题\2【jvm调优】jvm堆一直在飙升 还不是内存泄露.assets\2-7.png)

看起来都是正常的 第二个是Finalizer队列 jdbc链接执行的

![17542b5b3e8a2441e5fcc1a80cdc2a7](D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\8 面试场景题\2【jvm调优】jvm堆一直在飙升 还不是内存泄露.assets\2-8.png)

![9](D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\8 面试场景题\2【jvm调优】jvm堆一直在飙升 还不是内存泄露.assets\2-9.png)

深堆引用里都是这玩意  这图也糊了 其实就是Connection/J的  但是也不是啥产生内存泄露的东西

深堆是Java内存管理中一个与垃圾回收（GC）密切相关的概念，它主要用于描述对象被GC回收后可以真实释放的内存大小。以下是深堆的详细解释：

### 定义

- **深堆（Deep Heap）**：指一个对象的保留集中所有对象的浅堆大小之和。浅堆指的是对象本身占用的内存，不包括其内部引用对象的大小。因此，深堆实际上是通过该对象能够直接或间接访问到的所有对象的浅堆之和，即对象被回收后能够释放的真实空间。

### 关键概念

- **浅堆（Shallow Heap）**：一个对象结构所占用内存的大小，包括对象头、实例数据和对齐填充，但不包括其内部引用的其他对象的大小。
- **保留集（Retained Set）**：对象被垃圾回收后，可以被释放的所有对象的集合，即仅能通过该对象引用到的直接或间接的所有对象的集合。

### 深堆的计算

- 深堆的计算涉及确定一个对象的保留集，并计算该集合中所有对象的浅堆大小之和。
- 例如，如果对象A引用了对象B和C，而对象B又引用了对象D，那么对象A的深堆将包括A、B和D的浅堆大小之和（如果C不能通过A之外的其他方式被访问到，则C也属于A的深堆；否则，C的浅堆不计入A的深堆）。

### 实际应用

- 在Java内存分析和优化中，了解深堆的概念对于识别内存泄漏和优化内存使用至关重要。
- 开发者可以使用各种工具（如MAT、JVisualVM等）来分析Java堆内存中的对象及其深堆大小，以便找到占用大量内存的对象并进行相应的优化。

<img src="D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\8 面试场景题\2【jvm调优】jvm堆一直在飙升 还不是内存泄露.assets\2-10.png" alt="10" style="zoom:150%;" />

那可以得出一个结论 没内存泄露 系统正常  就是jvm参数没配明白

因为当时那系统 交接过来就压根没配置啥jvm gc参数 

```sh
-XX:+UseConcMarkSweepGC // 开启 cms gc
-XX:CMSInitiatingOccupancyFraction=80 // 老年代占用 80% 时进行 full gc
-XX:+UseCMSInitiatingOccupancyOnly // 让 cms gc 老实一点，用我们的配置来工作
-XX:+CMSClassUnloadingEnabled // 永久代卸载类时进行 cms gc 操作
可以看到，产生堆满的原因除了 gc 策略有问题外，还有一种可能：堆空间的老年代和年轻代的占用比例不合适，而对于 eden 和 survivor 的比例也不合适，调整之：
-XX:SurvivorRatio=6 //survior 占用比例
-XX:NewRatio=4 // 年轻代占用比例
-XX:MaxDirectMemorySize=1024M // 直接内存大小，这里我们的 jsf 使用了 netty，存在零拷贝，所以最好配置下直接内存
-XX:MaxTenuringThreshold=12 // 对象晋升老年代年龄
-XX:PretenureSizeThreshold=5242880 // 配置 5M 的大对象直接进入老年代，做分配担保
同时为了节省内存，开启了逃逸分析和标量替换：
-XX:+DoEscapeAnalysis // 开启逃逸分析
-XX:+EliminateAllocations // 开启标量替换
```

这是当时调整的参数

`visualvm` 和 `mat` 也很像 

![11](D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\8 面试场景题\2【jvm调优】jvm堆一直在飙升 还不是内存泄露.assets\2-11.png)

配置完了起码full gc动了 他也运行好一些了 堆没那么离奇了

![12](D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\8 面试场景题\2【jvm调优】jvm堆一直在飙升 还不是内存泄露.assets\2-12.png)

这个还是比较简单的 

![13](D:\Git\fanxy_knowledge_graph\Fanxy的学习笔记\8 面试场景题\2【jvm调优】jvm堆一直在飙升 还不是内存泄露.assets\2-13.png)

参数大小根据你实际的请求和业务产生内存对象分析   一般能让yong gc处理就处理了 所以年轻代可以适当调整比例 不过用默认的也行  理论上是压测觉得 不过我们当时没压测 那破系统没多少人用 就是业务复杂  你得通过压测 你知道你单机最大qps下 产生多少对象 分配多少内存  你了解你业务 你知道这些对象的活跃度 

精细点的话 可以用es的工具包统计你一个对象占内存空间 然后看看是不是需要分配担保  

90%对象 都在年轻代say byebye了  要么压缩年轻代空间 让他尽快gc 大部分对象都能立刻回收了

要么你发现晋升到老年代的多 那你也得让老年代回收起来 





