# Linux Commands

[TOC]

## open

<img src="/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.PtQQzZ/Pasted Graphic 1.png" alt="Pasted Graphic 1" style="zoom: 33%;" />

## touch

<img src="/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.N3K5g7/Pasted Graphic 2.png" alt="Pasted Graphic 2" style="zoom: 33%;" />

## echo

1. 往文件里写内容

   eg： 

   `touch f1`

   `echo "I am f1 file"`

2. 输出命令

   echo -n 不换行输出

   echo -e 转义后输出

## vim

[在iTerms中打开文件并查看编辑内容]

i进入编辑模式，esc退出编辑模式，:wq退出vim编辑器

## source

[使文件生效]

eg: source .bash_profile 

## pwd

[查看当前目录完整路径]

## cd/rm/mv/ls/ll/mkdir/cp/scp

[基本文件和文件夹操作]

1. cd切换目录
2. rm移除文件/文件夹(-r)
3. mv移动或者拷贝文件/文件夹
4. ls ll查看文件夹下所有文件
5. mkdir创建文件夹
6. cp拷贝文件/文件夹
7. scp远程拷贝： 
   1. scp /var/lib/stickyadstv/lite-stats/data/main-batch/1596555242 lugao@nycjump01.fwmrm.net:~/slog
   2. scp lugao@nycjump01.fwmrm.net:~/slog/1603247461.tar.zst lugao@stgdev03.stg.fwmrm.net:~/slog

## top

[查看进程状态，按q退出]

## ps aux | grep [pattern]

[ps查看当前进程状态]

[ps aux显示所有包含其他使用者的行程]

<img src="/Users/lugao/Library/Application Support/typora-user-images/image-20201024100356040.png" alt="image-20201024100356040" style="zoom:33%;" />

ps aux | grep [pattern]

```shell
查看进程信息
root@daily-report1-fw-us-east.stickyadstv.com[PROD:10.254.10.1]:~$ ps aux | grep grafana
root     19256  0.0  0.2 2024220 70564 ?       Sl    2020 194:50 ./grafana-server -config ../conf/defaults.ini

查看进程启动可执行文件
root@daily-report1-fw-us-east.stickyadstv.com[PROD:10.254.10.1]:~$ ll /proc/19256/cwd
lrwxrwxrwx 1 root root 0 Jun 17 04:24 /proc/19256/cwd -> /root/grafana-6.0.2/bin
```





## kill

kill -l 列出所有可用信号

kill -9 [PID] 彻底杀死一个进程

## zip/unzip

压缩和解压zip

gzip [XXX.gz] 压缩

gzip [XXX.gz] -d 解压缩

## tar

压缩和解压tar

tar -zxf [XXX.tar.gz] 对XXX压缩文件解压缩

tar -I zstd -xvf [XXX.tar.gz]  显示XXX文件使用zstd来解压缩的详细过程

zstdcat [XXX.tar.gz] | head -n 10 查看XXX压缩文件中的前10行内容

tar -I zstd -xvf 1617657422.tar.zst 

## less

[分页查看文件内容]

## tail -f

[实时最新查看文件末尾内容]

## netstat -nltp

[显示Linux机器的网络状态]

## crontab

1. crontab -l：展示crontab内容
2. crontab -e：编辑crontab内容

## ifconfig

[显示或设置网络设备]

ifconfig eth0：查看本机IP地址

## ssh

[通过ssh协议远程登陆服务器]

ssh-key实现免密登陆：[https://my.oschina.net/u/4395893/blog/3318413/print](https://my.oschina.net/u/4395893/blog/3318413/print) 
$ ssh-keygen -t rsa -C “email@email.com” -b 4096.  [目前我用的是qq邮箱]
$ ssh-copy-id -I ~/.ssh/id_rsa.pub user@serverIP

## grep

[查看文件中符合指定条件的行]

1. grep --color -m 100 "[pattern]" [filepath]：查看file中符合pattern的前100行
2. grep --color -E "[pattern1].*[pattern2]" [filepath]：查看file中符合pattern1和pattern2的行
3. grep --color "[pattern1]\\|[pattern2]" [filepath]：查看file中符合pattern1或pattern2的行
4. grep --color "[pattern1]" [filepath] | grep -v "[pattern2]"：查看file中符合pattern1且不符合pattern2的行
5. grep --color -C 5 "[pattern]" [filepath]：查看file中包含指定pattern的行的前后5行内容
   1. grep --color -B 5
   2. grep --color -A 5
6. grep -c ....... 计算符合样式的条数

## cut

剪切并摘取

例：echo "${filename}" | cut -d "." -f 3 只输出filename中以.分隔开的第3个字符串

## ln

硬连接： `ln f1 f2` 为f1创建硬连接f2

​	硬连接的两个文件之间内容是同步的，文件有相同的 inode 及 data block，只有删除所有相关文件的内容才算是真正删除了这个内容；硬连接的原文件是可以随意移动的

软连接：`ln -s f1 f3` 为f1创建软连接f3

​	软连接的两个文件之间内容是同步的，但f3只拥有f1的文件路径信息，因此如果删除了或者移动了原文件f1，那么f3也将毫无意义，内容也会失效

## sed

依照脚本的指令来自动处理和编辑文本文件

https://www.cnblogs.com/ggjucheng/archive/2013/01/13/2856901.html

## find

查找

find [dirname] -name [pattern] 列出路径下匹配的所有文件（-iname 忽略大小写）

find [filename] -type d -name [pattern] 列出路径下所有匹配的文件夹

## Others

1. du -sh [filepath/dirpath]：展示文件大小
2. ll [filepath] | wc -l：展示符合filepath的文件个数 (eg: ll *server* | wc -l)
3. lsof -i:[端口号]：查看某一端口的占用情况
4. 

## Systemctl

1. /lib/systemd/system/ 中创建新的service的配置文件，如：

```shell
[Unit]
Description=SE Avatar Engine Service
After=network.target

[Service]
ExecStart=/usr/share/stickyadstv/se-avatar-engine/bin/se-avatar-engine -c /etc/se-avatar-engine/config.yml
Restart=always

[Install]
WantedBy=multi-user.target
```

2. systemctl daemon-reload
3. systemctl start/stop/restart/status XXXXX

## lsof -i

查看端口占用

例如：lsof -i:9092



## df -h

查看磁盘空间

```shell
backup@clickhouse1-fw-us-east:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev             32G     0   32G   0% /dev
tmpfs           6.3G  210M  6.1G   4% /run
/dev/sda3       542G  4.2G  510G   1% /
tmpfs            32G     0   32G   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs            32G     0   32G   0% /sys/fs/cgroup
/dev/sdb1       4.4T  3.7T  488G  89% /export
/dev/sda1       114M   36M   69M  35% /boot
tmpfs           6.3G     0  6.3G   0% /run/user/0
tmpfs           6.3G     0  6.3G   0% /run/user/34
```



## history

```shell
root@daily-report1-fw-us-east.stickyadstv.com[PROD:10.254.10.1]:~$ history | grep grafana
  212  2020/10/22-03h22 : ps aux | grep grafana
  239  2020/11/02-01h05 : scp -i key.pem root@daily-report.stickyadstv.com:/root/grafana-6.0.2 ./grafana-6.0.2 -r
  240  2020/11/02-01h05 : scp -r -i key.pem root@daily-report.stickyadstv.com:/root/grafana-6.0.2 ./grafana-6.0.2
  296  2020/11/02-04h21 : cd /root/grafana/conf/
  358  2020/11/02-02h51 : vi 001-grafana.conf
  368  2020/11/02-02h53 : ln -s ../sites-available/001-grafana.conf 001-grafana.conf
  387  2020/11/02-04h49 : cd grafana
  392  2020/11/02-04h50 : ls|grep grafana
  393  2020/11/02-04h51 : history|grep grafana
  394  2020/11/02-04h51 : ./grafana-server -config ../conf/defaults.ini
  504  2020/11/02-20h59 : vi 001-grafana.conf
  506  2020/11/02-21h01 : vi 001-grafana.conf
  518  2020/11/02-21h04 : vi sites-enabled/001-grafana.conf
  689  2020/11/03-02h21 : cp 001-grafana.conf 003-looker.conf
 1447  2020/11/04-00h22 : cat 001-grafana.conf
 1458  2020/11/03-22h01 : vi /etc/apache2/sites-enabled/001-grafana.conf
 1880  2020/11/04-03h59 : vim 001-grafana.conf
 1889  2020/11/04-04h00 : vim 001-grafana.conf
 1894  2020/11/04-04h01 : vim 001-grafana.conf
 1966  2020/11/03-23h11 : vi 001-grafana.conf
 1969  2020/11/03-23h12 : vi 001-grafana.conf
 1971  2020/11/03-23h13 : vi 001-grafana.conf
 1978  2020/11/03-23h20 : vi 001-grafana.conf
 2018  2020/11/03-23h36 : vi 001-grafana.conf
 2021  2020/11/03-23h40 : vi 001-grafana.conf
 2028  2020/11/04-00h42 : cat 001-grafana.conf
 2096  2020/11/04-21h35 : vim 001-grafana.conf
 2144  2020/11/05-20h55 : vi /etc/apache2/sites-enabled/001-grafana.conf
 4582  2021/01/20-01h30 : ps aux | grep grafana
```

## Linux 文件目录

/etc 系统配置文件

/home  用户的主目录，在Linux中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的 

/root 存放root用户的文件

/lib 存放依赖库

/bin 存放常用命令

/usr 用户的应用程序和文件，类似于windows中的program-files

