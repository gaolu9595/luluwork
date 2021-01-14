## Keep them in my heart!!!

1. 多问多学，不要怕
2. 涉及到沟通的时候，预先想几个方案再去找别人
3. 涉及到跨组的大feature，需要预估risk，release时间需要进行组间沟通



一些踩坑

1. 涉及到dedup的时候一定要先清掉k-v的view文件再跑mapstats [太坑了]：view存放地址，见mapstats的conf文件
2. 当一个topic有多个partition时，若不指定mongo consumer来消费所有的partition，有可能消费不到实际存有数据的partition，导致mongo里面无法新建collection。
   解决：将对应redstats的conf中的consumed.partition.list=0注释掉
3. 在mapstats的conf文件中，processing下加入ignore.alloc.num=true，即可以每次运行同一个batch中的数据，不需要重复增加batch
4. command +shift+T 能找回chrome关闭之前的页面
5. DE的scribe log通过viewkey做哈希来分发到我们的Mapstats上
6. 