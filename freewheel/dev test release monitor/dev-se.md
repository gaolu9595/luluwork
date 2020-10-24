[TOC]



## 开发流程

1. github的dev主分支为develop，开发时先在本地拉出一个分支，然后commit&push到remote repo上，从该远程分支拉出一个pull request，继续开发，并由其他人进行code review
2. merge request被approved之后，将本分支merge到develop主分支上，等待release
3. 特殊情况hotfix：新建开发分支&pull request选择的base-branch为release分支，开发完成后直接merge到release分支上；在release之后，再将release分支merge到develop分支上

## SE 服务器使用指南

### MapStats

```
* ll /usr/share/stickyadstv/lite-stats/   项目jar包及依赖存放路径
* cat /etc/mapstats/mapstats.conf     mapstats配置文件
* cat /usr/share/mapstats/mapstats    启动脚本
* ll /var/lib/stickyadstv/lite-stats/data/main-batch/  DE-log存放路径 [BatchID][需要复制到自己的文件夹下查看具体内容]
* ll /var/log/mapstats/   mapstats的运行log存放目录
* /usr/share/mapstats/mapstats $BatchID  启动mapstats进程
* /etc/init.d/cron stop；/etc/init.d/cron start
* crontab -e； crontab -l 手动注释掉“定时启动mapstats进程”
```

### RedStats

```
* ll /usr/share/stickyadstv/lite-stats/   项目jar包及依赖存放路径
* ll /etc/redstats/$KEYNAME     redstats配置文件夹
* cat /etc/init.d/redstats   启动脚本
* ll /var/log/redstats/$KEYNAME    redstats的运行log存放目录
* /etc/init.d/redstats start $KEYNAME   启动相应key的redstats
* /etc/init.d/redstats stop $KEYNAME    停止相应key的redstats
* /etc/init.d/redstats status     查看redstats进程情况
```

### Mongo

```
* mongo -u bjo_user1 -p bjo_user1 127.0.0.1:27017/stats 连接mongodb数据库

*[staging上的mongo地址：10.235.2.40-10.235.2.42]
mongo -u datateamrw -ptae5Ahke 10.235.2.40:27021/stats
```

### MySQL

```
* 查bjo-dev上的mysql表来构建合法的log进行测试
```

### Kafka & Zookeeper

```
* ll /usr/share/kafka/bin/    kafka启动与停止脚本
* ll /usr/share/zookeeper/bin    zookeeper启动与停止脚本
```





