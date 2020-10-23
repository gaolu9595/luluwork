# SFX — Metrics
**sell side：/administrator/meta stats/advanced**
**buy side：/administrator/meta stats/buyer platform**

1. REQ：Ad Request总数 ::#au::
2. domain blocked：由于域名黑名单被block的Ad Request数（反欺诈）
3. whiteops blocked：由于whiteops检测被block的Ad Request数（反欺诈）::#wo::
4. buyer inv：邀请的buyer的总数（发出的bid request的总数 = 返回的bid response总数）::#br::
	1. bid done + bid failed + no bid = buyer inv.
	2. bid done：带有bid的bid response的总数::RECEIVED::
		1. bid filtered：因为限制条件被filter掉的bid response数::#bf::
			1. bid f.rate = bid filtered / bid done
			2. bid filtered = bid f.block-list  + bid f.floor-price + bid f.duration + bid f.ad-approval + bid f.multi-roll + bid f.non-secured-ad + bid f.creative-incompatibility + bid f.ad-incompatibility + bid f.mrm + bid f.epb-mapping
	3. bid fail：由于外部原因失败的bid response总数
		1. request error + response error + invalid currency + invalid bid + timeout = bid fail
		2. request error：
			1. 当检测到DSP因技术原因可能无法响应我们的bid request时，采用leaky bucket算法限流对这个DSP发送的bid request::CANCELLED::  
			2. 外部原因导致发送bid request失败，如timeout、socket连接失败、IO失败等::FAILEDSENDING::
		3. response error：外部原因导致接收bid response失败，如timeout、socket连接失败、IO失败等::FAILED::
		4. invalid currency：bid response出价所用的货币，与可选货币不兼容::FAILEDCURRENCY::
		5. invalid bid：bid response中的参数和Open-RTB协议不兼容，转参失败::FAILEDPARSING::
		6. timeout：	超时未收到bid response::TIMEOUT::
	4. no bid：不带bid的bid response总数::NOBID::
5. bid won：最终赢的bid的总数，bid won <=bid done ::#bs::
	1. win rate = bid won / bid done
6. REQ Country：来自PX所在国家的Ad Request总数
7. IMP：实际产生的impression总数
8. Fill Rate（STR）：impression / ad request
9. STR Tech：impression / bid won
10. VTR 0-100：广告被观看的时长比例  
	1. VTR = VTR100 / impression
11. Click：广告点击总数
	1. CTR = Click / impression
12. Revenue：SSP从DSP获取的钱
13. Cost：SSP给Publisher的钱
14. Margin：SSP给PX的钱（share）= (Revenue - Cost) / Revenue
15. eCPM：每1000次impression，SSP获得的revenue = (revenue/impression)*1000
16. shortfall price：【只在buy side有意义】该buy side因为某种filter规则而损失的bid的总价
17. view ability：impression的时间和空间的混合概念
	1. viewable impressions：可检测的符合IAB标准（展露50%的impression的前提下播2s以上）的impression数
	2. non-viewable impressions：可检测的不符合IAB标准的impression数
	3. view-ability rate =  viewable impressions / impression




