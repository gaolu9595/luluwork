# SE — Main WorkFlow
1. **Mapstats**: 
	* 创建DB连接，createDatabaseConnections
	* 读取从不同DE机器而来的Scribe Log文本，handleAllocator和handleBatchId和handleInputFileOptions （8G组成一个batch）
	* 每个file中的每一个line是一条log，将每一条log解析成一个特定eventType的logEvent， processEvents—readAndProcessAll—processOne—processLineWithParser—getLogEventsFromLine（用不同的IParser对象来转换成不同类型的LogAdServerEvent）
	* 对每个logEvent对象进行seat-mapping、external-ad-mapping和view-consolidation等处理，processEvent—processOneEventFailSafe—processOneEvent
	* 每个MotherTopic都会对某一个logEvent对象做Aggregation和DisAggregation操作，产生key-stats二元组，放入每台MapStats自己的guavaCache（aggregatedCache对象）中，将key-stats二元组依次写入一个blocking-queue时触发removalListener（此处可能会产生线程安全问题，onRemoval），然后通过ThreadedProducer写入Kafka，processOneEvent—aggregateStats/disaggregateStats—mapEvent/unmapEvent—不同的AMapper对象自己的processEvent/unprocessEvent
	* 在每一次对guava-cache做onRemovel的时候，会根据key的reduceTo方法为其所有的ChildTopic同步key-stats，然后通过ThreadedProducer写入Kafka（每个ChildTopic都有自己的MotherTopic，根据MotherTopic的key的reduceTo方法，可以在MotherTopic做aggregate和disaggregate的时候，顺带对ChildTopic做同样的事）
	* 写Kafka时，Kafka的topicName要根据key的name做进一步更新（根据valorized的true/false）
【可多线程：processLineWithParser（使用一个buffer，buffer满时一起submitTask并行执行）；processEvent（使用一个queue，queue满时一起执行）；】
【每台Mapstats上每个AMapper的Guava Cache（只对MotherTopic做缓存聚合） — 公共BlockingQueue（缓冲） — Kafka】
2. **Redstats**：
	* 创建Mongo连接
	* 为每一个kafka-topic创建一个特定的consumerGroup，根据redstats.conf来创建consumer（pass-through/lle/mongo等），createRunnableConsumer（每台redstats都是一个consumer，只消费一个topic的message）
	* 每台redstats上也有guava-cache对读取的kafka中的message做缓存聚合，keyDecoder和statsDecoder对message做解析，不同的removalListener来实现不同配置的Consumer的操作（只取TopK个记录来写Mongo，doBulkWrite（upsert又是一次聚合））
---
SE在DE和SFX中起到一个数据整合的作用：统计DE交易中的数据，用于SFX展示和DE复盘，也可用于一些知识发现和模式识别等

![D4DE6C57-B0FC-4254-8A1D-8E6CE67C4821](/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.UVj8PO/D4DE6C57-B0FC-4254-8A1D-8E6CE67C4821.png)