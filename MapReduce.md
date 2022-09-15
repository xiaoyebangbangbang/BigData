# 分布式计算框架MapReduce 简介
## MapReduce定义
  MapReduce是一种用于在大型商用硬件集群中（成千上万的结点）对海量数据（多个兆字节数据集）实施可靠的、高容错的分布式计算的框架，也是一种经典的并行计算模型。
## MapReduce基本原理
  MapReduce的基本原理是将一个复杂的问题（数据集）分成若干个简单的子问题（数据块）进行解决（Map函数）；然后对子问题的结果进行（Reduce函数），得到原有问题的解（结果）。如图所示

![](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimgsa.baidu.com%2Fexp%2Fw%3D500%2Fsign%3Dceb7a531c0bf6c81f7372ce88c3fb1d7%2Fd53f8794a4c27d1e802e7a9d17d5ad6eddc4385a.jpg&refer=http%3A%2F%2Fimgsa.baidu.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1665752061&t=c56622d431fe713e024cf58823ecba34)
# MapReduce 编程框架
  MapReduce编程模型主要由两个抽象类构成，即Mapper类和Reducer类，Mapper用以对切分过的原始数据进行处理，Reducer则对Mapper的结果进行汇总，得到最后的输出结果。对软件开发人员而言，只需要分别实现Map函数和Reduce函数即可以编写MapReduce程序，这一点和编写过程函数一样简单。
## Mapper
  Map阶段是一个独立的任务，由Mapper负责将输入的键值对（以下简称为“输入对”）映射到一组中间键值对。转换后的中间键值对不需要与原始的输入有相同的类型。给定的输入键值对可以映射到零或多个输出键值对（以下简称为“输出对”）。例如，<A, B> 这个键值对可以在 Map 阶段分别映射为 <A, 1> 和 <B, 1> 两个键值对。
## Reducer
  Map过程（包括了Combine）之后就是Reduce过程。Reduce过程利用Reducer共享键来减少中间值集合的规模。在作业中，reducer 的数量可以通过job.setNumReduceTasks(int)方法设置。具体而言，Reducer在实现时需要我们通过Job.setReducerClass(Class)方法来传递具体的作业代码，并可以通过重写覆盖该方法来完成初始化。框架将对分组后的输入中的每个 <key, (含有多个value的列表)> 调用 reduce(WritableComparable, Iterable<Writable>, Context) 方法。同样的，我们可以在应用程序中重写 cleanup(Context) 方法来执行清理工作。
### Reducer三个主要阶段
  分别是：shuffle、sort和reduce。
  + Shuffle：也称之为混洗，Mapper的排序输出是Reducer的输入。在这个阶段，MapReduce 框架通过 HTTP 协议获取所有 Mapper 输出的相关分区。
  + Sort：排序。由于不同的Mapper可能输出相同的键，因此在此阶段，框架将对 Reducer 的输入通过键进行分组。需要特别说明的是，Shuffle（混洗）和Sort（排序）阶段可能会同时发生。当获取到map的输出时，这两个阶段就会被合并进行。
  + Reduce：规约。在这个阶段将会对每一个<key, (含有多个value的列表)>记录都调用reduce(WritableComparable, Iterable<Writable>, Context)方法进行处理。作为最后一个阶段，Reduce任务的输出通常都通过Context.write(WritableComparable, Writable)方法写入到文件系统（无论是HDFS还是本地文件系统）。在这个过程中，Reducer的输出是无序的，我们也可以使用Counter来统计一些数据。 
## MapReduce编程实例
实例地址是： <https://blog.csdn.net/qq_41103843/article/details/121473263?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166315887316782395344720%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166315887316782395344720&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-4-121473263-null-null.142^v47^pc_rank_34_default_3,201^v3^control_2&utm_term=mapreduce&spm=1018.2226.3001.4187> 
# MapReduce 数据流
  从MapReduce的编程模型中可以发现，数据以不同的形式在不同结点之间流动，即经过本结点的分析处理，以另外一种形式进入下一个结点，从而得出最终结果。因此了解数据在各个结点之间的流入形式和流出形式对我们开发系统很重要。
## 分片、格式化数据源（InputFormat）
  InputFormat主要有两个任务，一个是对源文件进行分片，并确定Mapper的数量；另一个是对各分片进行格式化，处理成<key, value>形式的数据流并传给Mapper。
## Map过程
  Mapper接收<key, value>形式的数据，并处理成<key, value>形式的数据，具体的处理过程可由用户定义。
## Combiner过程
  每一个map()都可能会产生大量的本地输出，Combiner()的作用就是对map()端的输出先做一次合并，以减少在Map和Reduce结点之间的数据传输量，提高网络I/O性能，是MapReduce的一种优化手段之一 。 
## Shuffle 过程
  Shufle过程是指从Mapper产生的直接输出结果，经过一系列的处理，成为最终的Reducer直接输入数据为止的整个过程，这一过程也是MapReduce的核心过程。
## Reduce 过程
  Reducer接收<key. {value ist}>形式的数据流，形成<key, value>形式的数据输出，输出数据直接写入HDFS,具体的处理过程可由用户定义。
# MapReduce 任务运行流程
  MapReduce的任务流程是从客户端提交任务开始，直到任务运行结束的一系列流程。MRv2是Hadoop2中的MapReduce任务运行流程。在MRv2中，运行时环境由Yam提供，所以需要MapReduce相关服务和Yarn相关服务进行协同工作，下面先讲述MR2和Yarn的基本组成，再简述MapReduce任务的执行流程。
## MRv2基本组成
  MRv2舍弃了MRv1(是Hadopp中的MapRctce任务运行流程)中的JobTmd和TaskTack，而采用一种 新的MRAppMaster进行单一任务管理，并与Yarn中的ResoureManager和NodeManage协同调度与控制任务，避免了由单一服务（MRv1 中的JobTrack）管理和调度所有任务而产生的负载过重的问题。MRv2基本组成如下：
  + 客户端(client)：客户端用于向Yarn集群提交任务，是MapReduce用户和Yarn集群通信的唯一途径， 它通过ApplicationClientProtocol协议（RPC协议的一个实现）与Yarn的ResourceManager通信，通过客户端，还可以对任务状态进行查询或杀死任务等。客户端还可以通过MRClientProtocol协议（RPC协议的一个实现）与MRAppMaster（请看下一条）进行通信，从而直接监控和控制作业，以减轻ResourceManager的负担。
  + MR AppMaster: MRAppMaster 为ApplicationMaster的一一个实现，它监控和调度一整套MR任务流程，每个MR任务只产生一个MRAppMaster. MRAppMaster 只负责任务管理，并不负责资源的调配。
  + Map Task和Reduce Task: 用户定义的Map函数和Reduce函数的实例化，在MRv2中，它们只能运行在Yarn给定的资源限制下，由MRAppMaster和NodeMange协同管理和调度。
## Yarn 基本组成
  Yarn是一个资源管理平台，它监控和调度整个集群资源，并负责管理集群所有任务的运行和任务资源的分配，它的基本组成如下：
  + Resoure Manager (RM): 运行于NameNode,为整个集群的资源调度器，它主要包括两个组件: Resoure Schedule (资源调度器)和Aplications Manager (应用程序管理器)。
    + Resoure Schedule：当有应用程序已经注册需要运行时，ApplcationMaster会向它申请资源，而它会根据当时的资源和限制进行资源分配，它会产生一个container资源描述。
    + Applications Manager：它负责管理整个集群运行的所有任务，包括应用程序的提交，和Resource Schedule协商启动和监控ApplicationMaster, 并在ApplicationMaster任务失败时在其他结点重启它。
  + NodeManager：运行于DataNode，监控并管理单个结点的计算资源，并定时向RM汇报结点的资源使用情况，在结点上有任务时，还负责对container进行创建、运行状态的监控及最终销毁。
  + AplcatinMaster (AM):负责对一个任务流程的调度、管理，包括任务注册、资源申请、以及和NodeManage通信以开启和杀掉任务等。
  + Container：Yarn架构下对运算资源的一种描述，它封装了某个结点的多维度资源，包活CPU、RAM、Disk、Network等。当AM向RM申请资源时，RM分配的资源就是以cotaner表示的，Map task和Reduce Task只能在所分配的cntainer描述限制中运行。
## 任务流程
  在Yarn中，资源管理由ResourceManage和NodeManager共同完成，其中，Resource-Manager中的调度器负责资源的分配，NodeManager负责资源的供给和隔离。Resource-Manager将某个NodeManager上资源分配给任务(所谓的“资源调度”)后，NodeManager需按照要求为任务提供相应的资源，并保证这些资源具有独占性，为任务运行提供基础的保证(所谓的资源隔离)。
  Yarn架构中的MapReduce任务运行流程主要可以分为两个部分：一个是客户端向RsocoueManrer提交任务，ResourceManager通知相应的NodeManager启动MRApMaser；二是MRAppMaster启动成功后，则由它调度整个任务的运行，直到任务完成。其详细步骤如图所示。
![](https://upload-images.jianshu.io/upload_images/27324454-008af12d72d2638f.png?imageMogr2/auto-orient/strip|imageView2/2/w/770/format/webp)
