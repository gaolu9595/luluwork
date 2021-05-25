

# Mongo

**登陆**

```shell
mongo -u $USER -p $PASSWORD $LOCATION:$PORT/$DB
```

**创建collection**

```shell
db.createCollection(“$COLLECTION_NAME”, {$CONFIG})
```

**获取所有collection**

```shell
db.getCollectionNames()
```

**删除collection**

```shell
db.$COLLECTION_NAME.drop()
```

**Document 创建 删除 查询 更新**

```shell
db.$COL_NAME.insert({$INSERT})	
db.$COL_NAME.find({$CONDITION})	
db.$COL_NAME.remove({$CONDITION})
db.$COL_NAME.remove({})    删除全部documents
db.$COLNAME.update({$CONDITION}, {$UPDATE}, $IF_UPSERT, $IF_MULTI)
```

**查看collection的索引**

```shell
db.ea_seat_bpl_zone_deal_device_daily.getIndexes()
```

**判断某个key是否存在**

```shell
db.zone_integration_type_device_vpaid_daily.find({"value.rwg1":{$exists:1}})
```

**计数**

```shell
db.ad_bidder_zone_daily.count()
```

**聚合**

**mongo高级聚合** (https://www.cnblogs.com/zhoujie/p/mongo1.html)

```shell
db.ea_seat_bpl_zone_deal_device_daily.aggregate([
{$match:{
	"_id.d":{$gte:20210101,$lte:20210201},
	"_id.dv":5,"_id.di":"STI-USD-02358"
}},
{$group:{
	_id:"$_id.d",
	req:{$sum:"$value.r"}
}}
])
```

* $match用于条件过滤
* $group用于指定聚合的key

**嵌套js查询**

```shell
db.ad_bidder_zone_daily.find( { $where: function() { return (this._id.d == 20210426 && this.value.upct > this.value.ct )}}).pretty();
```

