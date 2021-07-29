# Free Talks

[TOC]

## 2020

### Kafka Sender之前的操作

producer client会**on-demand**建立起与kafka broker之间的连接

1. socket conn：producer和kafka node建立连接

2. handle conn异步NIO

3. producer向kafka node请求API 版本号

4. handle api-version异步NIO

5. producer将node状态置为**ready**

6. producer开始发送数据，handle send

   client调用send api，producer等到batch满了或者linear time到期以后，开始真正的sender

   send之前，要先决定发送到哪个blocker

### kafka metadata

https://blog.csdn.net/XU906722/article/details/104420085

metadata的更新是最高优先级的，stop the world

更新metadata和发送message，都是由Send线程（非用户线程）来负责的

### AR & ISR

简单来说，分区中的所有副本统称为 AR (Assigned Replicas)。 所有与leader副本保持一定程度同步的副本（包括leader副本在内）组成 ISR (In Sync Replicas)。 ISR 集合是AR 集合的一个子集

metadata没有本地存储

## 2021

### AWS Nouns

On-premise 运行在企业自建的、自己组织的环境中

Cloud 运行在远端的云环境中

EMR Flink (ASW Documents)，EMR推荐不要使用常驻集群

Scale Up    提高计算能力来实现扩展

Scale Out  提高服务器数量来实现扩展

### AWS TEST plans：

1. EC2性能（读写rocks db性能等）
2. EMR / Hadoop
3. HBase
4. Flink / Spark

### 数据处理的三个重点

资源是否够用

数据产品是否可以多样

运维是否方便
