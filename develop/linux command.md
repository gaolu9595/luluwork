# Linux Commands

[TOC]

## open

<img src="/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.PtQQzZ/Pasted Graphic 1.png" alt="Pasted Graphic 1" style="zoom: 33%;" />

## touch

<img src="/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.N3K5g7/Pasted Graphic 2.png" alt="Pasted Graphic 2" style="zoom: 33%;" />

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

## kill

kill -l 列出所有可用信号

kill -9 [PID] 彻底杀死一个进程

## zip/unzip

压缩和解压zip

## tar

压缩和解压tar

tar -zxf [XXX.tar.gz] 对XXX压缩文件解压缩

tar -I zstd -xvf [XXX.tar.gz]  显示XXX文件使用zstd来解压缩的详细过程

zstdcat [XXX.tar.gz] | head -n 10 查看XXX压缩文件中的前10行内容

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

## echo

输出命令

echo -n 不换行输出

echo -e 转义后输出



## sed

依照脚本的指令来自动处理和编辑文本文件

## find

查找

find [dirname] -name [pattern] 列出路径下匹配的所有文件（-iname 忽略大小写）

find [dilename] -type d -name [pattern] 列出路径下所有匹配的文件夹

## Others

1. du -sh [filepath/dirpath]：展示文件大小
2. ll [filepath] | wc -l：展示符合filepath的文件个数 (eg: ll *server* | wc -l)
3. lsof -i:[端口号]：查看某一端口的占用情况
4. 