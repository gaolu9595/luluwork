# SQL 


**SQL常用语句** (https://www.cnblogs.com/liu224/p/10754991.html%20)

**登陆**

```sql
mysql --user stickyadstv_rw --host 10.13.17.14 -P3307 -p
```

**查询表数据大小**

```sql
select DATA_LENGTH from tables where TABLE_NAME="external_ad"
select concat(round(sum(DATA_LENGTH/1024/1024),2),'MB') as data from tables where TABLE_NAME="external_ad"
```

**查询表数据行数**

```sql
select count(1) from external_ad
```

**查询外键依赖**

```sql
select table_name, column_name, constraint_name, referenced_table_name, referenced_column_name from information_schema.key_column_usage where constraint_schema="stickyads" and referenced_table_name="bidder";
+----------------------+-------------+-----------------------------+-----------------------+------------------------+
| table_name           | column_name | constraint_name             | referenced_table_name | referenced_column_name |
+----------------------+-------------+-----------------------------+-----------------------+------------------------+
| bidder_priority      | bidder_id   | fk_bidder                   | bidder                | id                     |
| buyer_actual_revenue | bidder_id   | buyer_actual_revenue_ibfk_1 | bidder                | id                     |
| deal_for_bidder      | bidder_id   | deal_for_bidder_ibfk_2      | bidder                | id                     |
| zone_for_bidder      | bidder_id   | zone_for_bidder_ibfk_2      | bidder                | id                     |
+----------------------+-------------+-----------------------------+-----------------------+------------------------+
4 rows in set (0.00 sec)
```

**添加外键约束**

```sql
alter table external_ad_mapping add CONSTRAINT `external_ad_mapping_ad` FOREIGN KEY (`ad_id`) REFERENCES `banners` (`bannerid`);
```

**随机查询几行**

```sql
SELECT * FROM `table` ORDER BY RAND() LIMIT 5
```

https://blog.csdn.net/zxl315/article/details/2435368

