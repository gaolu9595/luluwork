# Parquet & Presto 
**我的理解**
OLTP更适合使用MySQL这类面向行存储的关系型数据库，在使用索引的情况下可以很快地查询出所需行的所有信息用于逻辑处理；

OLAP更适合使用列存储的数据库，因为更快地做出各列的数据分析才是主要任务，列的数据存放在一起便于做各类统计（数据量大时列存储更能压缩存储空间；一般是有一定的缓存机制，利用好可以做到非常快的查询和统计）



**OLTP & OLAP** （前者是面向事务面向简单查询的；后者是面向分析面向复杂查询的，常以前者作为背后数据源）



**Parquet**是列式存储的一种格式 ，不是具体的一个技术框架或数据库
[深入分析Parquet列式存储格式-InfoQ](https://www.infoq.cn/article/in-depth-analysis-of-parquet-column-storage-format)

**Presto查询引擎**（Distributed SQL Query Engine）【可支持查询parquet】
[深入理解Presto - 知乎](https://zhuanlan.zhihu.com/p/101366898)