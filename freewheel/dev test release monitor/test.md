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

## Mapstats4-DEV测试

```shell
root@mapstats4-preprod.stickyadstv.com[PREPROD:10.235.2.47]:~$ cd test/ms/
root@mapstats4-preprod.stickyadstv.com[PREPROD:10.235.2.47]:~/test/ms$ ls
analysis.sh  batch.sh  concurrent_mapstats  consumer-group.sh  log  mapstats  mapstats.conf  spark  views
root@mapstats4-preprod.stickyadstv.com[PREPROD:10.235.2.47]:~/test/ms$ cat mapstats
#!/bin/bash
#dir_log="/var/lib/stickyadstv/file_kv_store/tempfile"
#cd $dir_log
#dest_file=$(ls -t |tail -n 1)
#tar zxf $dest_file -C /var/lib/scribe/main/
#rm -f $dest_file
#cd /root/

source /root/.credentials

LOG_DIR="/root/test/ms/log"
LOG="`date +%Y-%m-%d-%H:%M.log`"
GC_LOG="gc-`date +%Y-%m-%d-%H%M.log`"
DATA_DIR="/root/test/data"
# Unit size is KB (default block-size in du command)

sync
echo 1 > /proc/sys/vm/drop_caches

rm -rf views

if [ "$#" -eq 1 ]; then
    BATCHID="$1"
    INPUT_DIR="$DATA_DIR/$BATCHID"
    if [ ! -d "$INPUT_DIR" ]; then
        echo "$INPUT_DIR must exist and be a directory."
        exit 1
    fi
elif [ "$#" -ne 0 ]; then
    echo "Usage: $0 [batchid]"
    exit 1
fi

# Get CLASSPATH right
SE_HOME="/root/test"
CLASSPATH="/root/test/litestats.jar"
for file in $SE_HOME/lib/*.jar; do
  CLASSPATH=$CLASSPATH:$file
done

echo `md5sum /root/test/litestats.jar`

cd $LOG_DIR

  java -XX:NewRatio=1 -Xms24000m -Xmx24000m ${FLAMEGRAPH_JVM_OPTS} -verbose:gc -Dlog4j.configurationFile="file:/usr/share/stickyadstv/lite-stats/log4j.xml" \
    -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -Xloggc:$GC_LOG \
    -cp $CLASSPATH \
    com.adswizz.litestats.Main \
    --default \
    --batch-id $BATCHID \
    --input-dir "$INPUT_DIR" \
    --config-file /root/test/ms/mapstats.conf \
    --config-file-override /etc/mapstats/mapstats-override.conf >> "$LOG_DIR/$LOG" 2>&1
    RESULT="$?"
root@mapstats4-preprod.stickyadstv.com[PREPROD:10.235.2.47]:~/test/ms$ cd /root/test/data
root@mapstats4-preprod.stickyadstv.com[PREPROD:10.235.2.47]:~/test/data$ ls
1624300141  1624306381  1624309021  1624315741  1624317061  1624321681  1624328401
root@mapstats4-preprod.stickyadstv.com[PREPROD:10.235.2.47]:~/test/ms$ bash mapstats 1624317061
root@mapstats4-preprod.stickyadstv.com[PREPROD:10.235.2.47]:~/test/ms$ cd log
root@mapstats4-preprod.stickyadstv.com[PREPROD:10.235.2.47]:~/test/ms/log$ ls
2021-07-06-02:34.log  2021-07-06-04:10.log    gc-2021-07-06-0251.log  gc-2021-07-06-0429.log
2021-07-06-02:51.log  2021-07-06-04:29.log    gc-2021-07-06-0306.log  gc-2021-07-06-0529.log
2021-07-06-03:06.log  2021-07-06-05:29.log    gc-2021-07-06-0321.log  gc-2021-07-06-0531.log
2021-07-06-03:21.log  2021-07-06-05:31.log    gc-2021-07-06-0348.log
2021-07-06-03:48.log  gc-2021-07-06-0234.log  gc-2021-07-06-0410.log
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