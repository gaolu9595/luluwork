[TOC]

## Log查看

```
1. 检查mapstats是否有将stats写入Kafka的各个topic中
grep --color "CacheStat" 2020-10-20-22:35.log
2. 检查valorizer的这种consumer是否从Kafka中消费了数据，并且flushing到kafka中
grep --color "Flushing a buffer" server.log
3. 检查写mongo的这种consumer是否从Kafka中消费了数据，并且写到mongo中
grep --color "Bulk async result" server.log

```

## 单元测试UT

```
* 本地docker启动
* 跑单测cases
* 例1：单测涉及到loading mysql cache和各种mysql操作的case（如ExternalAdIdProcessorTest）
```

## BJO-DEV测试

```
* 配置并启动kafka和zookeeper
* 配置并启动mapstats，观察运行日志，观察mapstats是否正常运行，是否正常产生stats
* 配置并启动redstats，观察运行日志，观察mongo中对应的collection是否正确写入
```

## AB Test

- bjo-dev mapstats机器，目录：/home/abtest

```
* 启动abtest：/home/abtest/script/abtest_git.sh [branch_name]
* common-conf存放：/home/abtest/script/common.sh
* Scribe Log存放：/home/abtest/mapstats/data/main-batch/ [batch-id在common-conf中定义]
* diff文件：/home/abtest/compare
* mongo数据文件：/home/abtest/compare/redstats/[run-id]/standards&test
```
* 查询diff内容:
grep "!" 【#diff文件名】 | sort
* 查询对应log内容，查看diff是否合理



## 回归测试

如果jenkins任务失败遇到类似情况:

```
Step 1/5 : FROM arti-cache.freewheel.tv/openjdk:8
manifest for arti-cache.freewheel.tv/openjdk:8 not found
```

说明artifacotory的cache被清除了, 但是不知道为什么jenkins自己的pull不会触发artifactory主动缓存, 需要自己手动在其他机器上执行`docker pull arti-cache.freewheel.tv/openjdk:8` 然后再执行jenkins