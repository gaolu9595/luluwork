[TOC]

https://wiki.freewheel.tv/display/SSPTW/New+table+ea_seat_bpl_zone_deal_device_daily

## 修改配置文件

对比参考：

1. AdBidderZoneDomainCountryOsDeviceBrowserHourlyKey.conf
2. ExternalAdSeatBuyerPlatformZoneDealDeviceDailyKey9.conf

在config中加入clickhouse的连接信息，enable clickhouse写



## Code

1. 在src/tv.stickyads/redstats/clickhouse/schema中新增表
2. 在clickhouse bulk write的initialization中新增此schema



## Performance Test

### enviornment

1. Pre-prod MongoDB
2. Pre-prod Clickhouse

### insert test

1. 同等数据的插入执行时间对比（通过code logs）
2. 每一天插入同等数据条数，对比mongo和clickhouse实际存储的记录条数
3. 每一天插入同等数据条数，对比mongo和clickhouse实际存储的data size和index size

### query test

1. 线上常用query的查询速度对比
2. mongo without index / mongo with index，分别与clickhouse进行查询速度对比
3. slow query的查询速度对比