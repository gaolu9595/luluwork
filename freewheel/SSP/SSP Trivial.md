# SFX — Trivial
1. **Zone Routing**：在condition满足的情况下，允许publisher将ad重定向到另一个zone上。其中，Zone Routing的rule中配置的zone-id和zone-name就指示着目标zone。
2. **Ads.txt 与 App-Ads.txt**：IAB中的一种规范，防止非法的inventory sell。publisher将自己授权来卖所拥有inventory的seller的列表，存放在其root domain上，供DSP等查看验证。
3. **Domain Blockedlist and Whitelist**：SFX中设置了一些default blocked domains，这些domain一般是欺诈风险高等不安全的domain；PX内部可自己设置一些private blocked domains。
4. **OTT & CTV & VPAID**：都是DeviceType
	1. VPAID- 视频播放器广告界面定义
	2. OTT-Over The Top 联网转接电视视频播放，本质是一系列applications
5. **Buyer Group**：用于Publisher来为PX中的所有buyer进行分类、过滤、设置优先级等管理操作，用于invite和choose buyer中。
	1. 过滤时，按照default-shared-personal-custom的顺序；
	2. 判断优先级时，custom>personal>shared>default。
6. **Business Rules Evaluation**：
	* ZONE_ID
		* For Zone Routing
	* TRACKING_ID
		* For Custom Segments
	* FLOOR PRICE, FLOOR_PRICE_BUYER_SIDE, FLOOR_PRICE_SELL_SIDE, FLOOR_PRICE_BOTH:
		* For custom floor price
	* DEAL_ID
		* For deal targeting
	* BUYER_GRUOP
		* For custom buyers group
7. **VAST**：
 视频广告领域的一个AdServer和VideoPlayer之间的通信协议，规范了VideoPlayer对AdServer发起请求的uri格式等信息
	1. 具体介绍如  [https://wiki.freewheel.tv/display/SSPTW/SSP+Scenario+Training+Session+1+Followup](https://wiki.freewheel.tv/display/SSPTW/SSP+Scenario+Training+Session+1+Followup) 
	2. _格式举例如 _ [http://ads1-preprod.stickyadstv.com/www/delivery/swfIndex.php?reqType=AdsSetup&protocolVersion=2.0&zoneId=12096963](http://ads1-preprod.stickyadstv.com/www/delivery/swfIndex.php?reqType=AdsSetup&protocolVersion=2.0&zoneId=12096963) 
8. DE 的auction过程**本质上**就是，填充Bid Request Open-RTB字段 + 解析 Bid Response Open-RTB字段  
