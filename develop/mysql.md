# SQL 
**MySQL-Login**

![8497B877-3F81-4001-AB10-E309BB4497FA](/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.LVaoBK/8497B877-3F81-4001-AB10-E309BB4497FA.png)

**Links**

[SQL常用语句 - 不忘过往 - 博客园](https://www.cnblogs.com/liu224/p/10754991.html%20)

## Tips

1. -P 端口号， -p 密码 （这两者一定要区分好，否则会有冲突）
2.  select DATA_LENGTH from tables where TABLE_NAME="external_ad" 查询表数据大小
3. select concat(round(sum(DATA_LENGTH/1024/1024),2),'MB') as data from tables where TABLE_NAME="external_ad" 查询表数据大小
4. select count(1) from external_ad 查询表数据行数
5. 