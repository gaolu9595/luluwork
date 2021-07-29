# 数据处理架构

[TOC]

## Batch Processing

Hadoop

有界的不会改变的数据集

## Stream Processing

Flink、Storm、Spark

无穷的持续集成的数据集

## Lambda Architecture

结合批处理和流处理的架构

## Hadoop Ecosystem

【HDFS、MapReduce、HBase、Zookeeper、Hive、Yarn】



## Flink

refs：

https://ci.apache.org/projects/flink/flink-docs-release-1.13/zh/docs/concepts/flink-architecture/

### 1. 整体架构

![preview](https://segmentfault.com/img/remote/1460000016902817?w=781&h=462/view)

### 2. 基本数据流结构

![2018-10-14_09-51-09](https://segmentfault.com/img/remote/1460000016902819?w=1656&h=1108)

### 3. Confusions

（1）Process Function及其全局一致性和容错保障





## 4. Usage

https://segmentfault.com/a/1190000016901447 跟着走一遍

1. 安装 

   1. brew install apache-flink
   2. 默认路径：/usr/local/Cellar

2. 启动集群

   ```shell
   `lugao@lugao-mac /usr/local/Cellar/apache-flink/1.11.2/libexec/bin ./start-cluster.sh
   Starting cluster.
   Starting standalonesession daemon on host lugao-mac.
   Starting taskexecutor daemon on host lugao-mac.
   ```

   http://127.0.0.1:8081/#/overview 查看Flink服务

3. 提交作业

   ```shell
   lugao@lugao-mac /usr/local/Cellar/apache-flink/1.11.2/bin flink run -c SocketTextStreamWordCount /Users/lugao/lulu/FlinkDemoWordCount/target/FlinkDemoWordCount-1.0-SNAPSHOT.jar 127.0.0.1 9000
   Job has been submitted with JobID 66a0c17f454b85aef9ba0bf4f9f456cb
   ```

   `flink run -c [EntryPointClassOfProject] [ProjectJarPath]`

4. 查看作业情况

   1. http://127.0.0.1:8081

   2. ```shell
      lugao@lugao-mac /usr/local/Cellar/apache-flink/1.11.2/libexec/log tail -f flink-lugao-taskexecutor-0-lugao-mac.out
      ```

5. 停止集群

   ```shell
   lugao@lugao-mac /usr/local/Cellar/apache-flink/1.11.2/libexec/bin ./stop-cluster.sh
   ```

6. 































