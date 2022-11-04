# Hadoop

## 第一章 hadoop 概述

##### 1\. hadoop 是什么？

① hadoop 是 apache基金会开发的**分布式系统基础架构。**

② 主要解决，**海量数据的存储和海量数据的分析计算问题**

Tips: 广义上来说，Hadoop通常是指一个更广泛的概念——Hadoop生态圈。包括：Hbase,Hive zooker

##### 1.2 hadoop 发展史 

① **创始人 Doug Cutting** (基于 luence 开发)

② 面临的困难，存储海量数据，检索海量数据速度

③ 图标**玩具象**

##### 1.3 hadoop 三大发行版本

- Apache (基础版本) 2006
- Cloudera 2008 CDH
- Hortonworks 2011 HDP

Hortonworks 被 Cloudera 收购推出新产品 CDP

##### 1.4 hadoop 的优势

① 高可靠性：Hadoop底层维护多个数据副本，所以即使Hadoop某个计算元
素或存储出现故障，也不会导致数据的丢失。

② 高扩展性：动态增加/删除节点

③ 高效性：并行工作，加快任务处理速度

④ 高容错性：能够自动将失败的任务重新分配。

##### 1.5 hadoop 的组成（总i但）

hadoop 1.x

- Common (辅助工具)
- HDFS (数据存储)
- MapReduce (计算+资源调度)

hadoop 2.x

- Common (辅助工具)
- HDFS (数据存储)
- MapReduce (计算)
- Yarn (资源调度)

Hadoop 3.x 在组成上没有变化

1\. HDFS 架构概述

Hadoop Distributed File System, 简称 HDFS（Hadoop 分布式文件系统）

- NameNode： 记录数据存储的位置（元数据） 

- DataNode: 存储数据

- Secondary NameNode (2NN)：辅助 NameNode 工作 (每隔一段时间对NameNode 元数据)

2\. Yarn 架构概述

Yet Another Resource Negotiator 简称YARN ，另一种资源协调者，是Hadoop 的资源管理器。

- ResourceManager (RM): 管理集群资源（内存、CPU 等）
- NodeManager (NM): 管理一个节点资源
- ApplicationMaster (AM): 管理单个任务运行
- Container: 容器，（任务运行的地方）里面封装了任务运行所需要的资源，如内存、CPU、硬盘、网络等。 

Tips: ![Yarn 架构](Yarn%20%E6%9E%B6%E6%9E%84.png)

① 客户端可以有多个

② 集群可以运行多个 ApplicationMaster

③ 每隔 NodeManager 可以有多个 Container

3\. MapReduce 架构介绍

MapReduce 将计算过程分为两个阶段：Map 和Reduce

1）Map 阶段 **并行处理输入数据**
2）Reduce 阶段 *对Map 结果进行汇总*

4\. HDFS、Yarn、MapReduce 三者关系

##### 1.6 大数据生态体系

![大数据生态体系](%E5%A4%A7%E6%95%B0%E6%8D%AE%E7%94%9F%E6%80%81%E4%BD%93%E7%B3%BB.png)

## 第二章 Hadoop 运行环境搭建
