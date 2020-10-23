# DE — DataDog Logs

https://wiki.freewheel.tv/display/SSPTW/SSP+T2+Knowledge+-+Datadog+Logs
https://wiki.freewheel.tv/display/~lugao/DE+Process**（以这个流程图为准）**

## 使用方法
1. 某个zone的production环境下的VAST地址+“&debug=logInfo&pls=1”
2. inspect进入network模块找到debug的Req_No
3. Datadog/Logs中search：@req:Req_No
4. Logs展示了DE在这个session中处理ad_setup_request的整个流程逻辑

## 日志Details
1. Http和GDPR-Consent等context informations
2. validate the request data
3. 向WhiteOps发合规验证的request，生成Scribe **::#wo::**
```
WO IVT request:
1. userId > viewerId
2. impressionId > viewkey
3. supplierId > PX(agency)
4. publisherId > publisher
5. mediaType
6. deviceType
```
4. Audience details retrieved  from Cassandra【从DMP处获取user相关的信息】
5. preAuction callbacks ?
6. evaluate business rules
```
ZONE_ID、TRACKING_ID、PRICE、DEAL_ID和BUYER_GROUP这五种group的rule条件，优先级由高到低。
DE将依次对PX内每个group中的rule进行逐个的匹配验证，最终得到applied rules
```
7. 域名domain的黑名单判断和处理
8. 开启auction，每个auction对应着自己的一个view-key
9. 邀请和丢弃buyer【ad-served和open-rtb】
```
1. 被invite的buyer根据自己的target规则来决定是否participate
2. evaluate Internal Bidder 是否有ad来link到该zone，选出候选ads
	 Internal Bidder指该zone所属PX创建的内部buyers(direct buyer/ad-served buyer/sold-by-publisher buyer)
3. targeting到BuyerPlatforms，确定要invite的buyers和buyer platforms
4. bid throttling进行节流 （可用到一些机器学习的方法）
5. 给候选ads背后的internal bidder和OpenRTB的bidder都发bid request，获取bid response
```
10. 以上操作会生成Scribe **::#br::**
11. 关闭auction
12. 对bid response进行evaluate、filter和select，继而生成Scribe **::#bs::**和Scribe **::bf::**
13. 对最终获胜的bidder发送ad request，生成Scribe **::#rq::**
14. 未在datadog log中展示的生成Scribe Log的过程，都是在ad播放过程中回调DE进行的，如VTR、Impression、Click和Viewability等

## 注意⚠️
1. 当zone有内部连接的ad时，也会给其他openrtb的bidder发送bid-request，按照UI上配置的priority来进行selecting
2. Auction的流程解析 见：https://wiki.freewheel.tv/display/SSPTW/Auction+Process
3. Internal Buyer：包含Direct Buyer和Ad-Served Buyer两种，本质上是假的buyer，事先已经在我们的OLTP中book好了相关的数据信息
4. DE 的Fixed Buyer：也就是SFX上的Third Party Buyer，需要两层逻辑来获取真实的ad的url
5. 在Buyer Group的rule evaluating时，最终只会根据priority选出一个buyer group
6. 在进行Deal的rule evaluating时，只要buyer匹配Deal并且buyer本身的targeting条件也匹配的话，buyer就会被invite，不会受buyer group或者其他规则条件的限制
7. 最终的clear price = second higher price + 0.001
8. DE最终会给player返回一个VAST地址，其中包含着DSP返回的VAST信息+我们自己的impression callback等信息，用于内部记录impression过程【因为：player会在广告播放过程中，直接去call VAST中的这些impression callback进行反馈，不会再经过我们系统】 