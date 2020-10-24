# Monitoring - Grafana
NY5环境常用监控： https://wiki.freewheel.tv/display/SSPTW/NY5+Environments

1. kafka：[Grafana](https://monitoring.stickyadstv.com:3000/d/rRlQbfJZz/kafka-production-speed-ny5?refresh=15m&orgId=1&var-group_name=All)
2. mapstats：[Grafana](https://monitoring.stickyadstv.com:3000/d/nwXMJB1Zk/mapstats-ny5?orgId=1)
3. redstats：https://monitoring.stickyadstv.com:3000/d/csHeXh3iz/redstats-lags-atkafka1-new?refresh=5m&orgId=1

- General/DataTeam
```
* kafka生产与消费速度
* kafka延迟（lag）
* mapstats执行job的时间（执行速度：新加坡>NY5>RBX欧洲）
* mapstats执行错误（由log情况导入）
* redstats执行错误（由log情况导入）
	（1）Bulk write errors with category != duplicated key 【不是因为重复主键而引起的插入mongo错误】
	（2）Tried to read two times the same message 【在onRemoval的时候，重复读取同一份数据】
* kafka打开文件数目 （一般是会在30K上下，不超过40K问题不大）
* mapstats和redstats启动时间
```
---
- General/Mapstats
```
* mapstats的job处理速率、并发数等
* 磁盘 /var文件夹下的使用情况（DE-log、Run-log...）
* 磁盘 /var/lib/stickyadstv-file_kv_store使用情况（主要存放View）
* Mapstats Cache Load Time
* 剩余待处理的Scribe日志数（8G为一个处理batch，少于8G都没问题）
* Log中的Info、Error和Warn统计
```
---
- General/Redstats Lags (@Kafka1)
```
* Kafka Lag延迟状况
* 不同Conf的Redstats的kakfa延迟
```
---
- General/Kafka Production Speed 
- General/Kafka Production Speed - NY5
```
* group选择：要选加group后缀的
* kafka生产速度、消费速度
* kafka消费速度-生产速度
* kafka延迟
```
---
- General/System Overview
```
* 不同机房的机器状况
* CPU、Memory、Disk、Load、Network、IO等
```