# Kafka Knowledge

[TOC]

## Kafka Sender之前的操作

producer client会**on-demand**建立起与kafka broker之间的连接

1. socket conn：producer和kafka node建立连接

2. handle conn异步NIO

3. producer向kafka node请求API 版本号

4. handle api-version异步NIO

5. producer将node状态置为**ready**

6. producer开始发送数据，handle send

   client调用send api，producer等到batch满了或者linear time到期以后，开始真正的sender

   send之前，要先决定发送到哪个blocker



## metadata

https://blog.csdn.net/XU906722/article/details/104420085

metadata的更新是最高优先级的，stop the world

更新metadata和发送message，都是由Send线程（非用户线程）来负责的



## AR & ISR

简单来说，分区中的所有副本统称为 AR (Assigned Replicas)。 所有与leader副本保持一定程度同步的副本（包括leader副本在内）组成 ISR (In Sync Replicas)。 ISR 集合是AR 集合的一个子集





metadata没有本地存储

