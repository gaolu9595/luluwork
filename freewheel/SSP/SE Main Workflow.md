# SE — Main WorkFlow
1. **Mapstats**: 
	* 创建DB连接，createDatabaseConnections
	* 读取从不同DE机器而来的Scribe Log文本，handleAllocator和handleBatchId和handleInputFileOptions （8G组成一个batch）
	* 每个file中的每一个line是一条log，将每一条log解析成一个特定eventType的logEvent， processEvents—readAndProcessAll—processOne—processLineWithParser—getLogEventsFromLine（用不同的IParser对象来转换成不同类型的LogAdServerEvent）
	* 对每个logEvent对象进行seat-mapping、external-ad-mapping和view-consolidation等处理，processEvent—processOneEventFailSafe—processOneEvent
	* 每个MotherTopic都会对某一个logEvent对象做Aggregation和DisAggregation操作，产生key-stats二元组，放入每台MapStats自己的guavaCache（aggregatedCache对象）中，将key-stats二元组依次写入一个blocking-queue时触发removalListener（此处可能会产生线程安全问题，onRemoval），然后通过ThreadedProducer写入Kafka，processOneEvent—aggregateStats/disaggregateStats—mapEvent/unmapEvent—不同的AMapper对象自己的processEvent/unprocessEvent
	* 在AMapper对Event做完aggregate和disaggregate以后，会进入postProcessing函数，里面会要求flushData（flush aggregateCache里面的元素）
	* 在每一次移除guava-cache（aggregateCache）中元素的时候，会调用onRemovel函数，会根据key的reduceTo方法为其所有的ChildTopic同步key-stats，然后将元素flush到一个buffer中，当buffer满或者达到一定时间间隔时，通过ThreadedProducer写入Kafka（每个ChildTopic都有自己的MotherTopic，根据MotherTopic的key的reduceTo方法，可以在MotherTopic做aggregate和disaggregate的时候，顺带对ChildTopic做同样的事）【AggregationRemovalListener】
	* 写Kafka时，Kafka的topicName要根据key的name做进一步更新（根据valorized的true/false）
	【可多线程：processLineWithParser（使用一个buffer，buffer满时一起submitTask并行执行）；processEvent（使用一个queue，queue满时一起执行）；】
	【每台Mapstats上每个AMapper的Guava Cache（只对MotherTopic做缓存聚合） — 公共BlockingQueue（缓冲） — Kafka】
	* valorized的配置：
	* true：aggregationRemovalListener会将聚合后的stats从cache写入带valorizer后缀的topic中，经由valorizer redstats处理完以后再由valorizedStatsRemovalListender写入到不带valorizer后缀的topic中，最终由redstats处理入库。
	* false：aggregationRemovalListener会将聚合后的stats从cache写入不带valorizer后缀的topic中，由redstats处理入库。
2. **Redstats**：
	* 创建Kafka连接
	
	* 每一台redstats都是不对等的，配置着自己的多个topic，即启动多个redstats进程（⚠️redstats名称和配置的topic name是child topic，也就是最终写入mongo的collection名字，与kafka上的mother topic不一定一致）
	
	* 在每一个redstats进程上，为自己的redstats-child-topic创建出特定的consumerGroup，根据redstats.conf来创建consumer（pass-through/lle/mongo等），createRunnableConsumer（每个redstats进程都先创建一个consumer group，然后创建出一个对应的consumer来消费kafka，而ACachedConsumer是Consumer对象的父类）
	
	* 一个redstats进程有一个ConsumerGroup，包含一个consumer线程去消费指定kafka topic指定partition的数据（我们采用的是custom assign的模式）；多个redstats进程的同名ConsumerGroup消费同一kafka topic的数据
	
	* 每一个consumer消费的kafka topic（⚠️mother topic）在ChildTopic.Name属性里面已经配置好了，consumer线程会被assign去消费对应kafka topic的指定partition的数据
	
	* 每个redstats进程里（每个consumer group）也有guava-cache对读取的kafka中的message做缓存聚合，keyDecoder和statsDecoder对message做解析（⚠️key为kafka topic，处理message时，会再进一步reduceTo到此redstats进程的child topic上，然后再写入mongo或clickhouse等等）
	
	* 当guava-cache满了，进行removal。onRemoval请求valorizer重写进kafka，或者bulk write到指定的后端数据库（先merge然后flush）……（根据redstats consumer的配置不同，其removalListener的行为也不一样）
	
	* 
	
	* 只取TopK个记录来写Mongo，doBulkWrite（upsert又是一次聚合）
	
	  
---
SE在DE和SFX中起到一个数据整合的作用：统计DE交易中的数据，用于SFX展示和DE复盘，也可用于一些知识发现和模式识别等

## Mapstats View Processor

1. 相关的class：
   1. ViewProcessor，入口类
   2. View，View的basic fields定义类
   3. Bid
   4. BidHistory
   5. Roll

2. 相关的操作：
   1. getView【获取ViewCache中当前event的View的信息】
   2. deduplicate
      1. isDuplicated？判断当前event是不是duplicated了（判断方式因event type而异）
      2. view.deduplicate 拿到初步的duplicated event（注意：bid-selected等类型的dup event是historical event，而其他类型的dup event是current event，因此需要重新创建一个新的toDisaggreg来作为后续unmap的event）
      3. roll.deduplicate根据不同的event type来更新toDisaggreg中除basic fields之外的其他特定fields
   3. update
      1. 更新一些过期的值
   4. valorizeEvent：对#imp event的valorize fields进行验证，缺失的valorize fields需要使用historical的#bs中相对应的fields来进行替换【原来我们SE只对BuyerType为Open-RTB的情况进行这个操作，经过FW-45459以后，SE将对Ad-Served的情况也同样进行这样的操作，来支持SMI Margin的准确计算】

