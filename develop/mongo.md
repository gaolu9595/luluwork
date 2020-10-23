# Mongo
**::Mongo-Login::**
mongo -u $USER -p $PASSWORD $LOCATION:$PORT/$DB
![77A31264-3B8E-4AAF-850E-F8D569F68793](/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.fSrLWm/77A31264-3B8E-4AAF-850E-F8D569F68793.png)

**::Mongo基本操作::**

```
Collection 创建 删除 查询
	db.createCollection(“$COL_NAME”, {$CONFIG})
	db.getCollectionNames()
	db.$COL_NAME.drop()
```

```
Document 创建 删除 查询 更新
	db.$COL_NAME.insert({$INSERT})	
	db.$COL_NAME.find({$CONDITION})
	db.$COL_NAME.remove({$CONDITION})
	db.$COL_NAME.remove({})    删除全部documents
	db.$COLNAME.update({$CONDITION}, {$UPDATE}, $IF_UPSERT, $IF_MULTI)
```

**::Mongo实用命令::**

* 判断value是否存在：db.zone_integration_type_device_vpaid_daily.find({"value.rwg1":{$exists:1}})
* 计数：db.ad_bidder_zone_daily.count()
* 聚合： 
mongos>db.ad_bidder_zone_blockrule_daily.aggregate([{$match:{"_id.d":20201015}},{$group: {_id:"$_id.bri”, num_tutorial:{$sum:1}}}])
mongos> db.ad_bidder_zone_daily.aggregate([{$match:{"_id.d":20201014}},{$group:{_id:null, request_sum:{$sum:"$value.r”}}}])
	* $match用于条件过滤
	* $group用于指定聚合的key


