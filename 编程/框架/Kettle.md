## 第一章 Kettle 概述

后缀名：

- ktr：kettle transformation
- kjb: kettle job

转换（transformation）完成针对数据基础的转换（并行）

作业 （job） 完成对整个工作流的控制（串行）

Tips: Kettle (现在已经更名为**PDI**，Pentaho Data Integration-Pentaho数据集成

1\. 转换(transformation)和作业（job）的区别：

① 作业是步骤流，转换是数据流

② 作业的每一个步骤必须等到前面的步骤都跑完了，该步骤才会运行。而转换会一次性把所有的控件全部启动，一个控件对应启动一个线程，而数据流会从第一个控件开始，一条记录，一条记录的流向最后的控件。

2\. 转换

**转换**(transaformation)负责数据的输入、转换、校验和输出等工作。各个步骤使用**跳** (Hop) 来链接。Kettle 中数据的最小单位是数据**行**（row），数据流中流动其实是缓存的**行集** (RowSet) 。

3\. 作业

**作业** (Job)，负责定义一个完成整个工作流的控制。因为转换（transformation）以并行方式执行，所以必须存在一个串行的调度工具来执行转换，这就是 Kettle中的作业。