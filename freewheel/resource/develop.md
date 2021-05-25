# Develop Resource in FW

1. **跳板机地址**

```python
user：lugao ---免key直接登陆
password：llkk@9595

prod-jumpbox-1      nycjump01.fwmrm.net
prod-jumpbox-2      nycjump02.fwmrm.net
stg-dev-jumpbox     stgdev03.stg.fwmrm.net
```

2. **STG环境**

```python
user: root
跳板机：stgdev03.stg.fwmrm.net

mapstats01-preprod       root@mapstats01-preprod.sa.fwmrm.net
mapstats02-preprod       root@mapstats02-preprod.sa.fwmrm.net
mapstats03-preprod       root@mapstats03-preprod.sa.fwmrm.net
mapstats04-preprod       root@mapstats04-preprod.sa.fwmrm.net

redstats01-preprod       root@redstats01-preprod.sa.fwmrm.net
redstats02-preprod       root@redstats02-preprod.sa.fwmrm.net
redstats03-preprod       root@redstats03-preprod.sa.fwmrm.net
redstats04-preprod       root@redstats04-preprod.sa.fwmrm.net

root@kafka01-preprod.sa.fwmrm.net     10.235.2.52 9092 2181
root@kafka02-preprod.sa.fwmrm.net     10.235.2.53
root@kafka03-preprod.sa.fwmrm.net     10.235.2.54
root@kafka04-preprod.sa.fwmrm.net     10.235.2.55
root@kafka05-preprod.sa.fwmrm.net     10.235.2.56

[mapstats1上可以登mysql]
mysql:mysql --user stickyadstv_rw --host 10.235.2.44 -p
[{env:MYSQL_PASSWORD 在root/.credentials里面}]

[redstats1上可以登mongo]
mongo：mongo 10.235.2.40:27021/stats -u datateamrw -ptae5Ahke
[{env:MONGO_PASSWORD 在root/.credentials里面}]
10.235.2.40:27021
10.235.2.41:27021
10.235.2.42:27021

```

3. **NY5 PROD环境**

```python
跳板机1: nycjump01.fwmrm.net
跳板机2: nycjump02.fwmrm.net
user：backup

backup@mapstats[1-300]-fw-us-east.stickyadstv.com   
不同的mapstats分布在不同的DC，定时启动跑一样的mapstats任务（生产者）
backup@redstats[1-50]-fw-us-east.stickyadstv.com    
不同的redstats机器，常run，跑的是不同的redstats任务（消费者）
backup@kafka[]-fw-us-east.stickyadstv.com  
尽量不用，不归我们管理

tech@backup1.stickyadstv.com 可以查看production mysql的DBSchema（/var/backups/backupMysql/）

phase1 mapstats机器
mapstats6-fw-us-east.stickyadstv.com
mapstats83-fw-us-east.stickyadstv.com
mapstats221-fw-us-east.stickyadstv.com
mapstats284-fw-us-east.stickyadstv.com
【phase1时，只上四台mapstats，上所有的redstats】
【phase2时，上所有的mapstats】

prod的mapstats上的Scribe Log备份存放在这个目录下：
/var/lib/stickyadstv/file_kv_store/archives_uploaded

production环境的Mongo，主要包含stats和unique两个数据库
从daily-report连：mongo -u statsreadonly --password=zaem0eiY 10.254.18.2:27021/stats

production环境的mysql，可通过LQS查询
production环境的#Scribe Log，可通过LQS查看
```

4. **BJO-DEV环境**

```python
user:lugao
password：mac开机密码
需要sudo su进入root用户

bjo-ssp-mapstats    bjo-ssp-mapstats.dev.fwmrm.net
bjo-ssp-redstats    bjo-ssp-redstats.dev.fwmrm.net
bjo-ssp-kafka01     bjo-ssp-kafka01.dev.fwmrm.net
bjo-ssp-kafka02     bjo-ssp-kafka02.dev.fwmrm.net
bjo-ssp-kafka03     bjo-ssp-kafka03.dev.fwmrm.net
```

5. **Daily-Report**

```
1. 登prod跳板机
2. ssh root@daily-report2-fw-us-east.stickyadstv.com    （10.254.10.2）
3. clickhouse-client
【daily-report上的clickhouse，是通过azkaban调度任务来定时从prod mongo读取数据，再通过datax写入到clickhouse中的】
【daily-report上的clickhouse数据更新会有延迟，因此一般用于自己测试等等】
【从daily-report可以连到线上的mongo：mongo -u statsreadonly --password=zaem0eiY 10.254.18.2:27021/stats】
```

6. **Prod Clickhouse**

```
1. 登prod跳板机
2. ssh backup@10.254.15.[200-205]   [已经从RBX迁移到NY5, 为backup@clickhouse1-fw-us-east]
3. clickhouse-client --user default --password fUoEKuXY
cat /etc/clickhouse-server/mysql_dictionary.xml [从mysql中导入dicts]
【clickhouse1-3是数据的主要存储机器，4-6为副本backup】
【local表只存一个分区的数据，而基于多个local表的dist表能查到全量的数据】
4. sudo su - root [用root用户来查询修改clickhouse配置]
```

7. **Staging Clickhouse**

```
root@clickhouse1-preprod.stickyadstv.com[PREPROD:10.235.2.57]
clickhouse-client --user default --password freewheel
```

8. **Build机器**

```
1. 登staging跳板机
2. ssh root@build-preprod.stickyadstv.com
```

9. **SFX Prod**
   1. zfdang
   2. 8i68j7at
   
10. **STG mysql**，本地tableplus连接（把数据库映射到127.0.0.1:33060）

    ```
    ssh  -L  33060:10.235.2.29:3306 -N lugao@stgdev03.stg.fwmrm.net -p 22 -f
    ```

    

