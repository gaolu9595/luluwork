# Parquet & Presto &ClickHouse
我的理解：
OLTP更适合使用MySQL这类面向行存储的关系型数据库，在使用索引的情况下可以很快地查询出所需行的所有信息用于逻辑处理；

OLAP更适合使用列存储的数据库，因为更快地做出各列的数据分析才是主要任务，列的数据存放在一起便于做各类统计（数据量大时列存储更能压缩存储空间；一般是有一定的缓存机制，利用好可以做到非常快的查询和统计）

**OLTP & OLAP** （前者是面向事务面向简单查询的；后者是面向分析面向复杂查询的，常以前者作为背后数据源）

**Parquet**是列式存储的一种格式 ，不是具体的一个技术框架或数据库
[深入分析Parquet列式存储格式-InfoQ](https://www.infoq.cn/article/in-depth-analysis-of-parquet-column-storage-format)
![E370BEF6-C5FE-4190-8F8B-8E191F5C3D3B](/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.jgVT4w/E370BEF6-C5FE-4190-8F8B-8E191F5C3D3B.png)

![978A74BC-156F-42CF-9BC1-A27B130A7C7F](/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.89FJAB/978A74BC-156F-42CF-9BC1-A27B130A7C7F.png)

![D0C09FD6-07F5-45F6-A947-29ACB85081A9](/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.OVMyDU/D0C09FD6-07F5-45F6-A947-29ACB85081A9.png)

**Presto查询引擎**（Distributed SQL Query Engine）【可支持查询parquet】
[深入理解Presto - 知乎](https://zhuanlan.zhihu.com/p/101366898)
![480B0FD2-7316-4DB9-ABE4-C8E200AB80AB](/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.HybJ7u/480B0FD2-7316-4DB9-ABE4-C8E200AB80AB.png)

**ClickHouse**从OLAP场景需求出发，定制开发了一套全新的高效列式存储引擎，并且实现了数据有序存储、主键索引、稀疏索引、数据Sharding、数据Partitioning、TTL、主备复制等丰富功能。以上功能共同为ClickHouse极速的分析性能奠定了基础。