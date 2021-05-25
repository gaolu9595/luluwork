# ELK Monitors

[TOC]

## Avatar-Engine的log需求和格式

1. level=error

```
key cases:
  (1)time="" level=error msg="failed to XXXXXXXXXXXXXXXXXXX"
	(2)time="" level=error msg="some problems happen when XXXXXXXXXXXX"
	(3)time="" level=error msg="error XXXXXXXXXXXX"
	(4)time="" level=error msg="NET ERROR OCCUR XXXXXXXXX"
	(5)time="" level=error msg="Kafka Partition Consumer XXXXXXX"
	(6)有一种类似于“query uniq bidder: XXXXXX"的error，也通过filebeat收集，但是暂时不关心
	
	【end with ',' '(' ':'】
	
key indexes:
	timestamp/level/error-content
	
key monitors:
	(1)一段时间内的error数
	(2)error的出现
```



2. monitor=elk

```
key cases:
	(1)time="2021-03-15T04:36:37-04:00" level=info msg="kafka consume message from pid 0, offset=4" monitor=elk
	(2)time="2021-03-15T04:36:37-04:00" level=info msg="receive proto message:{external_ad_id: 1103409_0, nurl: test-nurl, adm: test-vast-xml, click_through: test-click-through, agency_id: 19, ad_type: 0, date: 2021-03-12 05:55:17}" monitor=elk
	(3)time="2021-03-15T04:36:37-04:00" level=info msg="duplicate external_ad/rechecked_external_ad" monitor=elk
	(4)time="2021-03-15T04:36:37-04:00" level=info msg="oltp known external_ad: 1103409_0" monitor=elk
	(5)time="2021-03-15T04:36:37-04:00" level=info msg="oltp no-need-recheck external_ad: 1103409_0" monitor=elk
	(6)time="2021-03-15T04:36:37-04:00" level=info msg="message processing latency: 21023.763" monitor=elk
	(7)time="2021-03-15T04:36:37-04:00" level=info msg="partition 0/1 finally/regularly commit XXXXXXXXXX" monitor=elk
	(8)time="2021-03-15T05:15:07-04:00" level=info msg="successfully processed unknown external_ad/rechecked_external_ad: 1333665_XmOkse4hne_XRco9L2wu3_XRco9L2wu3 (processor.go)"

key indexes:
	(1)timestamp/level/msg-content
	(2)timestamp/level/msg-content
	(3)timestamp/level/msg-content/dupExAd/dupReExAd
	(4)timestamp/level/msg-content/cache-miss-exAd
	(5)timestamp/level/msg-content/cache-miss-reExAd
	(6)timestamp/level/msg-content/latency
	(7)timestamp/level/msg-content/partition,commit-num,commit-event

key monitors:
	(1)kafka消费的消息数变化图
	(2)处理的消息数变化图
	(3)duplicate的exAd和reExAd数变化图
	(4)cache miss的exAd数变化图
	(5)cache miss的reExAd数变化图
	(6)latency报警，letancy变化图（要用到正则）
	(7)每个partition的commit num变化图（要用到正则，这个指标暂时不需要，没有导入es）
```



## ELK的整个配置流程

https://wiki.freewheel.tv/pages/viewpage.action?pageId=99877454#HowtoUseELK-WriteLogstashConfig

1. app程序产生log
2. 写filebeat的配置文件，使log进入local kafka（elk集群），通过mirror maker进入aws global kafka（elk集群）

<img src="/Users/lugao/Library/Application Support/typora-user-images/image-20210315111819093.png" alt="image-20210315111819093" style="zoom:50%;" />

https://jira.freewheel.tv/browse/OPS-87835

3. 写logstash的配置文件，使log从kafka中消费出来，并且进行一定的处理后，将必要的一些key-value存储到elastic search中

https://jira.freewheel.tv/browse/FW-51076

https://jira.freewheel.tv/browse/OPS-88071

4. 使用kibana新建一个index来展示我们需要的log



## filebeat日志收集【input & output】

example：

```shell
filebeat.inputs:
-
  paths: "/var/log/se-avatar-engine/server.log"
  include_lines: ['.*']
  fields:
    log_type: "fw-ssp-se-avatar-log"
  ignore_older: 0
  scan_frequency: 5s
  registry_file: /var/lib/filebeat/registryoutput:
  kafka:
    hosts: ["nycmq01.fwmrm.net:9092","nycmq02.fwmrm.net:9092","nycmq03.fwmrm.net:9092","nycmq04.fwmrm.net:9092"]
    topic: "fw-ssp-se-avatar-log"
    partition.round_robin:
      reachable_only: false
    required_acks: 1
    compression: gzip
    max_message_bytes: 900000
    codec.json:
      pretty: true
    version: 0.10.0logging:
  level: error
  to_files: true
  to_syslog: false
  files:
    path: '/var/log/filebeat'
```



## logstash日志处理【input & output & filter】

https://www.jianshu.com/p/96246125494d

https://doc.yonyoucloud.com/doc/logstash-best-practice-cn/filter/grok.html

http://grokdebug.herokuapp.com/

template模版，自定义一些分词规则，自定义数据在es里面的存储方式



### 测试环境测试

https://wiki.freewheel.tv/display/Infrastructure/How+to+Use+ELK#HowtoUseELK-TestingonDEVEnv

1. 登陆staging跳板机

2. 第一次登陆，需要将密钥[fw1-dev-logstash](https://wiki.freewheel.tv/download/attachments/144112023/fw1-dev-logstash?version=1&modificationDate=1575965104000&api=v2)上传到跳板机，我上传在.ssh/目录下

3. ```shell
   chmod 600 .ssh/fw1-dev-logstash
   ```

   

4. ```shell
   ssh -i .ssh/fw1-dev-logstash centos@10.23.42.78
   ```

   

5. 在centos@ip-10-23-42-78.ec2.internal机器的～/lugao/文件夹下放置自己的conf和template

6. ```shell
   /home/centos/kafka_2.10-0.10.2.2/bin/kafka-topics.sh --create --zookeeper zookeeper1.dev.aws.fwmrm.net/elkkafka --replication-factor 1 --partitions 4 --config compression.type=gzip retention.ms=7200000 --topic fw-ssp-se-avatar-log
   ```

   

7. 在conf文件里，dev环境需要额外配置output到elastic search的user和password

8. 可以fake几条logs，发送到dev kafka相应的topic中，然后观察logstash日志，在kibana上查看结果；或者直接设置logstash的输入为file，查看logstash处理file中内容的情况

```
/home/centos/kafka_2.10-0.10.2.2/bin/kafka-console-producer.sh --broker-list elkkafka.dev.dev.aws.fwmrm.net:9092 --topic fw-ssp-se-avatar-log
```

9. debug logstash

```shell
/usr/share/logstash/bin/logstash -f /home/centos/lugao/fw-ssp-se-avatar-log.conf --path.data=/home/centos/lugao/data/ --path.logs=/home/centos/lugao/logs/
```



## elastic search日志存储【倒排索引】

https://www.cnblogs.com/chenqionghe/p/12464671.html



## Kibana使用

https://www.cnblogs.com/chenqionghe/p/12503181.html



## Kibana Monitor Dashboard











