# 分布式计算框架MapReduce 简介
## MapReduce定义
MapReduce是一种用于在大型商用硬件集群中（成千上万的结点）对海量数据（多个兆字节数据集）实施可靠的、高容错的分布式计算的框架，也是一种经典的并行计算模型。
## MapReduce基本原理
MapReduce的基本原理是将一个复杂的问题（数据集）分成若干个简单的子问题（数据块）进行解决（Map函数）；然后对子问题的结果进行（Reduce函数），得到原有问题的解（结果）。如图所示

![](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimgsa.baidu.com%2Fexp%2Fw%3D500%2Fsign%3Dceb7a531c0bf6c81f7372ce88c3fb1d7%2Fd53f8794a4c27d1e802e7a9d17d5ad6eddc4385a.jpg&refer=http%3A%2F%2Fimgsa.baidu.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1665752061&t=c56622d431fe713e024cf58823ecba34)
# MapReduce 编程框架
MapReduce编程模型主要由两个抽象类构成，即Mapper类和Reducer类，Mapper用以对切分过的原始数据进行处理，Reducer则对Mapper的结果进行汇总，得到最后的输出结果。对软件开发人员而言，只需要分别实现Map函数和Reduce函数即可以编写MapReduce程序，这一点和编写过程函数一样简单。
## Mapper
Map 阶段是一个独立的任务，由Mapper负责将输入的键值对（以下简称为“输入对”）映射到一组中间键值对。转换后的中间键值对不需要与原始的输入有相同的类型。给定的输入键值对可以映射到零或多个输出键值对（以下简称为“输出对”）。
例如，<A, B> 这个键值对可以在 Map 阶段分别映射为 <A, 1> 和 <B, 1> 两个键值对。
## Reducer
Map过程（包括了Combine）之后就是Reduce过程。Reduce过程利用Reducer共享键来减少中间值集合的规模。在作业中，reducer 的数量可以通过job.setNumReduceTasks(int)方法设置。具体而言，Reducer在实现时需要我们通过Job.setReducerClass(Class)方法来传递具体的作业代码，并可以通过重写覆盖该方法来完成初始化。框架将对分组后的输入中的每个 <key, (含有多个value的列表)> 调用 reduce(WritableComparable, Iterable<Writable>, Context) 方法。同样的，我们可以在应用程序中重写 cleanup(Context) 方法来执行清理工作。
### Reducer三个主要阶段
分别是：shuffle、sort和reduce。
#### Shuffle
也称之为混洗，Mapper的排序输出是Reducer的输入。在这个阶段，MapReduce 框架通过 HTTP 协议获取所有 Mapper 输出的相关分区。
#### Sort
排序。由于不同的Mapper可能输出相同的键，因此在此阶段，框架将对 Reducer 的输入通过键进行分组。需要特别说明的是，Shuffle（混洗)和Sort（排序）阶段可能会同时发生。当获取到map的输出时，这两个阶段就会被合并进行。
#### Reduce
规约。在这个阶段将会对每一个<key, (含有多个value的列表)>记录都调用reduce(WritableComparable, Iterable<Writable>, Context)方法进行处理。作为最后一个阶段，Reduce任务的输出通常都通过Context.write(WritableComparable, Writable)方法写入到文件系统（无论是 HDFS 还是本地文件系统）。在这个过程中，Reducer的输出是无序的，我们也可以使用Counter来统计一些数据。 
## MapReduce编程实例
实例地址是： <https://blog.csdn.net/qq_41103843/article/details/121473263?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166315887316782395344720%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166315887316782395344720&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-4-121473263-null-null.142^v47^pc_rank_34_default_3,201^v3^control_2&utm_term=mapreduce&spm=1018.2226.3001.4187> 
# MapReduce 数据流
