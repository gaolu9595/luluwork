# NHT Notes can be found here 💯

## Glossaries

1. SSP： Supply-Side-Platform 
2. DSP： Demand-Side-Platform，也称为BuyerPlatform
3. Ad Exchange(Ad EX): 连接SSP和DSP的中间交易平台
4. Publisher：SSP的客户，拥有内容和流量
5. Content Owner: 网站内容的出版方
6. Marketer/Buyer：DSP的客户，需要投放广告
7. Campaign：广告投入的计划（主要来自于Buyer方），可能包含很多Ad Orders
8. Trading Desk：Buyer采用的一些专门的agency，这些agency使用DSP进行广告位的采买等（有时候buyer本身就是一个trading-desk）
9. Direct Buy: DSP或者Trading Desk和Publisher的inventory之间根据Campaign来进行直接采买，不走OpenRTB
10. ADS： Ad Decision Service
11. Site / Zone/ Slot: 广告位的物理位置
12. DMP：Data-Management-Platform
13. SEAT：DSP方内部的概念，给予每个Trading Desk管理的席位
14. DEAL：预先设定好的一些优质内容广告位的贩卖
15. OpenRTB：开放性竞价
16. Bidder：是对Trading Desk、DSP和SEAT三者的封装，主要提供给我们系统内部用
17. Ad-ID： Buyer Platform（DSP）、Creative和Bidder的组合，是DSP关心的内容
18. View：一个Ad Request过程中产生的所有类型的log日志的集合，主要是提供给用户回溯，增加透明度
19. Traffic: 流量

## Advertising Field

 1. ad features：non-personal、communication、persuasive

 2. advertiser(budget 💰) —> media(包含 ad-inventory 广告库存)

 3. media-type【media本身有内容】： print报纸、radio广播、tv、digital-online

    1. presentation：eg. Stream（类似于朋友圈广告）、banner、video、textual（搜索广告）、native原生广告（由广告内容驱动，例如电视剧植入）
    2. purpose-type：brand-awareness/direct-response

    5. 计费模式：电视广告（收视率）、品牌广告（CPM）、效果广告（CPC、CPA、CPS）
    6. GRP、TRP
    7. 广告行业发展进程：合约广告、定向广告（受众粗粒度定向）、竞价广告（价格不预先设定、精准定向、GSP广义第二高价、点击率预测、反作弊）、实时竞价(Read time bidding，比竞价广告更加动态化)/Programmatic程序化交易【Notice：竞价广告 不等于 平时常见的搜索竞价排名广告】
    	1. 参考链接： http://www.xiaoyunhua.com/1586.html   
    8. 程序化交易：SSP服务于inventory（管理预测广告库存，对接DSP或AdExchange）、DSP服务于advertiser（管理advertise，对接SSP或AdExchange）、AdExchange（接入所有DSP和SSP，提供交易平台）、第三方监管平台、DMP（数据管理平台，用于分析用户画像、audience-targeting）

---
## Product & Architecture
1. Linear and Digital TV

---

## MRM UI 
1. definition：a web platform for clients to manage their advertising business (客户主要是publisher)
2. CRO（）、creative（广告物料内容）
3. CRO设置一些自己的video元信息、要求restriction，管理ad-slot【Network Module】
4. campaign广告计划战役、广告订单录入、budget和price 【Advertising Module】
5. CRO的网站内置FW-SDK，FW-SDK发送Ad-Request到FW-backend获取合适的ad地址
6. HyLDA：Hybrid Linear/Digital Ads   定制化广告与动态广告混合【HyLDA Module】
7. 【大部分是publisher-advertiser合约订单，另一部分是空闲广告位需要reseller帮忙管理】，CRO、Content-Owner、Distributor（例如YouTube）、Reseller  【Partner Module】
8. 库存预测、实际报表信息查看【Insight Module】
9. 如果还有空闲广告位，CRO线下对接好DSP后，进行程序化交易，Market【Programmatic Module】

---
## AdServer
1. managing（广告物料、CMS->content-management-system->Network Module、OMS->order-management-system->Advertising Module）
2. serving (ad decision engine， 狭义上的AdServer）
3. tracking (Insight Module)
4. OpenRTB：国外程序化交易的标准协议，SSP和DSP的接入协议
5. Decision Engine
	1. Parse and collect information （content、slot->strategy/slot-position、platform->device-type、personal->geo-data/id-graph、user-state->historical-behaviors）
	2. Ad targeting(倒排索引、树结构）
	3. Ad Filtering（内容、预算、数量、格式……）
	4. Ranking（针对特定的优化目标，如maximum CRO‘s interest）<- Ad Priority优先级/Ad Score评分【（CPM*OSI*受众面大小）/ 持续时间】  <— OSI = current delivery budget/expected delivery budget
	5. Delivery Ads
	6. Log、Callback and Complete Decision
6. Partner Module ： MRM Rule  —  Reseller  —  mirror

---
## Forecasting
1. Transactional Management广告投放过程中的预测
2. Inventory Management广告库存预测

---
## Data Platform【infra-data】
1. Batch/Real-time data input, do data process, provide data access tools.   
2. BinLog: request / ack / callback [一个request会对应一个或多个ack]
3. Data Pipeline [Kafka (local\global) / SQL on Streaming]，每个Ad-Server机器都有自己的local-kafka，最后汇聚到global-kafka
4. Data Accessibilities 

---
## Linear Business
1. 两个概念：programmer（如Fox、NBC、ABC）和operator/MVPD（如Comcast）
2. STB（Set Top Box机顶盒，联电缆）、OTT（Over the top，如小米盒子，联网）
3. 电视广告的评价度量：收视率（如：尼尔森公司）
4. 电视广告投放的基本生态
	1. Operator从programmer中分出20%的广告位，可以对接一些区域广告主来将广告投放在它的势力范围内；根据观众个人信息，可以在机顶盒层用用户感兴趣的广告覆盖替换一些不适合用户的广告（TV Addressable）【可根据基本信息和house-hold策略来分析个人喜好】
	2. Programmer的80%广告对接的一般是通用的广告主

---
## Engineering Tools （Software Engineering)
1. SaaS软件（Software as a Service）：Software as a service (*SaaS*) is a software distribution model in which a third-party provider hosts applications and makes them available to customers over the Internet
2. Mindsets：IterativeDevelopment、IncrementingDevelopment、……
3. Test Strategy：单元测试（Unit Test）、Component Test、Integration Test、System Test、Manual Test
4. Unit Test很重要

---
## SSP
1. SMI   EPB
2. Bid guarantee deal
3. PG Deal
4. EPB Deal（附加一层额外的信息）
5. ROI

---
## Software Development Process
1. ENG-OPS 
2. SDLC (Software Development LifeCycle): 
	1. Waterfall（瀑布）—> 适用于大型软件
	2. Agile（增量式的重复执行每个sprint）—> 灵活、适应市场能力强
	3. Based on their combined experience of developing software and helping others do that, the seventeen signatories to the manifesto proclaimed that they value:
			* *Individuals and interactions*/over processes and tools/
			* *Working software*/over comprehensive documentation/
			* *Customer collaboration*/over contract negotiation/
			* *Responding to change*/over following a plan/ 
3. Scrum: Scrum is one of many in the agile model. [Develop Team/ Product Owner/ Scrum Master]
4. Burn down Chart 燃尽图

---
## MRM Rules （Digital）
1. Partners: ContentOwner / Content Rights Owner / Distributor / Reseller
	1. Reseller
		1. Full Reseller (是我们MRM中的客户） 【MRM to MRM】
		2. LightWeight Reseller （不是我们MRM的客户） 【Reseller Placement】
2. Contract Setting：

![4F4D7FA2-9375-4384-B7F0-2D2EB07DBFB7](/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.LXGhsi/4F4D7FA2-9375-4384-B7F0-2D2EB07DBFB7.png)

 	1. MRM Rule Priority优先级
     	1. 只允许Reseller管理广告位【完全屏蔽掉其他的rules】
     	2. Reseller优先管理，Reseller没卖完的广告位交由CRO自己管理
     	3. Reseller和CRO的direct-sold广告自由竞争，根据价格等
     	4. 只允许CRO自己管理广告位，没卖完的才交由Reseller
     [image:7A571D62-26A5-4198-8CEA-42CE8F4422F3-25086-00018AA719098D21/5755EBC7-226A-4829-9CCB-9C2E7E2C0AB7.png]
     4.  同一种实体，对于不同的角色有不同的表现形式
     ![5755EBC7-226A-4829-9CCB-9C2E7E2C0AB7](/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.wsSgcM/5755EBC7-226A-4829-9CCB-9C2E7E2C0AB7.png)
     outbound：配置CRO与哪些Reseller进行合作，配置一些rules，可以控制CRO的Ad Request的定向
     inbound：配置Reseller拥有的那些ad与哪些MRM rules相连，可以控制不同广告的投放