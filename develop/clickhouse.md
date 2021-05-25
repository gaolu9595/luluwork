# Clickhouse && Looker

**Clickhouse**：列式存储的分布式数据库，适用于OLAP场景

​	https://clickhouse.tech/docs/zh/

**Looker**：一个Business Intelligence Tool

​	Looker model其实可以看作是对clickhouse做的一层sql查询的封装

​	Looker中的view和clickhouse中的表结构是一一对应的，可以在looker的view中再自定义一些算法和展示方式



## materialized view

**物化视图**：

​	与view不同，materialized view实际存储着数据。它是远程数据的一个查询结果的本地副本，相当于是一个可以被刷新refresh的快照snapshot。**这样查询数据，就不用关联一大堆表，如果表很大的话，会在临时表空间内做大量的操作。**

**物化视图的类型：ON DEMAND、ON COMMIT**
	二者的区别在于刷新方法的不同，ON DEMAND顾名思义，仅在该物化视图“需要”被刷新了，才进行刷新(REFRESH)，即更新物化视图，以保证和基表数据的一致性；而ON COMMIT是说，一旦基表有了COMMIT，即事务提交，则立刻刷新，立刻更新物化视图，使得数据和基表一致

[View其实和table是一样的结构，只是view是对table进行了一层查询得到的。普通的view只有表结构，没有真实数据；物化view则实际存储着数据]

clickhouse创建物化视图：https://www.cnblogs.com/xibuhaohao/p/13667243.html

**当物化视图由几个数据源聚合而成，那么仅当左表更新时，物化视图才能更新数据**



## mysql字典

配置文件：/etc/clickhouse-server/mysql-dictionary.xml

1. key默认是按主键，但是当主键是string类型时需要配成complex key
2. 当想要按多个field进行dictGet时，需要在字典中配置complex key
3. 



## clickhouse语法

和sql语法很像

**查询clickhouse-server的config**

```shell
cat /etc/clickhouse-server/config.xml
```

**查询clickhouse-server运行的log及error log**

```shell
vi /var/log/clickhouse-server/clickhouse-server.log
vi /var/log/clickhouse-server/clickhouse-server.err.log
[/[pattern]：查找pattern所在位置，按n继续查找下一行]
```

**查询clickhouse集群的配置信息**

```sql
select * from system.clusters
```

**更改列的默认值为nullable**

```sql
alter table `default`.tttt MODIFY COLUMN name Nullable(String) --成功
```

**删除数据**

```sql
alter table `default` delete where [condition]
```

**更改表名**

```sql
rename table `old-name` to `new-name`
```

**创建M View**

```sql
CREATE MATERIALIZED VIEW stats.test_mv
(
    `date` Int64,
    `px_id` Int32,
    `zone_id` Int32,
    `revenue` Float64
)
ENGINE = MergeTree
ORDER BY (date, px_id, zone_id)
SETTINGS index_granularity = 8192 AS
SELECT
    basic.d AS date,
    basic.y AS px_id,
    basic.z AS zone_id,
    COALESCE(SUM((basic.rv + basic.rvb) / 10000000), 0) AS revenue
FROM stats.ad_bidder_zone_domain_country_os_device_daily AS basic
LEFT JOIN stickyads.sticky_market_place_mapping AS smpm ON basic.z = smpm.dest_zone_id
LEFT JOIN stickyads.sticky_market_place AS smp ON smpm.sticky_market_place_id = smp.id
GROUP BY
    basic.d,
    basic.y,
    basic.z,
    smp.selling_agency_id,
    smpm.zone_id,
    dictGet('bidder', 'buyer_platform_id', toUInt64(bri)),
    dictGet('bidder', 'trading_desk_id', toUInt64(bri)),
    basic.dv
```

**创建Dist表**

```sql
CREATE TABLE stats.test_mv_dist
(
    `date` Int64,
    `px_id` Int32,
    `zone_id` Int32,
    `revenue` Float64
)
ENGINE = Distributed('stats', 'stats', 'test_mv') 
【分布式：cluster name，database name，table name】
```



## Clickhouse中的Local表和Dist表

https://www.cnblogs.com/yisany/p/13524018.html

https://www.jianshu.com/p/20639fdfdc99

