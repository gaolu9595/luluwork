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

