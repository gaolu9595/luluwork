# SSP General Business Sessions
## General
**PX(private exchange)** 是我们的客户账号，代表着publisher端的利益

* Publisher Side
	* publishers  ：PX旗下管理的一些publishers内容商
	* sites ：每个publisher下面的一些sites网站
	* zones ：每个site下面的一些zones广告位
```
每个zone都有自己的VAST（Video Ad Serving Teplate） url；

该zone的video-player向stickyads服务器发送ad-setup-request之后，stickyads返回一个vast，zone将解析这个vast，播放广告并发送相应的track请求
```

* Demand Side
	* buyers ：分为Ad-Served Buyer、OpenRTB Buyer两大块
```
Ad-Served Buyer、Direct buyer以及sold-by-publisher buyer是PX内部使用的internal buyer，需要PX在自己账户中添加和信息补全
（系统会为每个internal bidder创建一个对应的buyer platform，这是虚构的）

OpenRTB Buyer是Private Exchange间通用的，通过SeetMapping在Admin账户中添加，配置一些targeting条件，然后进行buyer和PX之间的🔗（open-rtb buyer的buyer-platform是真实存在的，即DSP）

SEAT Mapping就是为了创建open-rtb的buyer，需要唯一确定buyer-platform、trading-desk、media-buyer等信息

SE中的SEATProcess是在遇到一个我们unknown的SEAD_ID时，在mysql中创建一条To_Complete字段为0的记录，通过SFX手动validate将To_Complete设为1

Third-party Buyer本质上是属于Ad-Served Buyer的
```
	* buyer groups ：主要是OpenRTB中的概念
```
buyer groups分为三个级别：default、publishers related和custom，优先级依次升高

buyer groups是PX自己配置的，主要是为了在ad-setup-request的时候过滤掉那些不想invite的buyers【OpenRTB buyers也可配置自己的targeting条件，过滤掉那些不想要receive的ad-setup-request】
```
	* campaigns ：Ad-Served和OpenRTB中都需要用到的概念
```
campaign：主要就是广告的投放战役计划，是ad端的一个重要概念，在我们的系统中每个ad都会与一个campaign🔗起来

不论是Ad-Served还是OpenRTB模式，SFX都会记录campaign的执行情况，最终拿着这些相关的reports数据去结账
```
	* ads : Internal Ads和External Ads
```
Internal Ads：Ad-Served模式中配置的，SFX中已知的ad。PX会在设置Campaign的时候，手动将ad和campaign🔗起来

External Ads：OpenRTB模式中由DSP返回的那些ads，在选中后会在系统中自动去创建ad和其对应的campaign🔗
```
---

## AdServed	 [大头的流量]
* 线下：publisher线下与advertiser协商好广告投放方案，并将合同内容配置在SFX上（如配置ad-served buyer、insertion order、campaign、ad等）
* SFX：将相应的zone和campaign🔗起来，作为最高优先级来进行广告投放

---

## OpenRTB [长尾的流量]
* Advertiser：广告主
* Media Agency：Advertiser委托的广告代理机构
* Trading Desk：Media Agency使用的专业交易平台
* DSP：Demand Side Platform，供Trading Desk来管理其广告投放
【在SFX中，DSP = Buyer Platform】
* SEAT：Trading Desk和DSP的组合标识。类似于PX在SFX中的身份，是Trading Desk在DSP中的唯一账号
【在SFX中，SEAT = Bidder = OpenRTB buyer】
```
open auction: 完全programmatic的广告投放模式
	不带deal ：无private-acution字段
	带normal deal：private-auction=0

private auction: 只考虑Deal约束而不考虑不带Deal约束的buyer的programmatic的广告投放模式【BidRequest中外部wseat=[]】
	private-auction字段=1
	guarantee deal属于private auction的范畴

deal：🔗publisher和DSP之间的一种约束，是多对多的关系【deal分为normal deal和guaranteed deal两种，区别在于OpenRTB中private_auction字段是0或是1】

deal和ad-served的区别是：ad-served提供的ad是确定的，在我们系统中预先设置好的（强信任）；deal则不一定是确定提供ad的，且这种ad仍然是通过programmatic进行投放的external-ad，且多对多的关系使得被deal连接的buyers仍然需要按照价格等其他条件进行竞争；而guaranteed-deal这种private auction情况下，可以理解为用程序化的方式来进行ad-served

在一次open auction的programmatic delivery过程中，DE会给deal hiited的DSP发带normal deal的bid request，同时给deal unhitted的DSP发送普通open auction的bid request（出于尽可能让这个auction能被bid成功的考虑）。一般来说，在带normal deal的bidder的bid response与其他bidder的bid response都符合targeting的情况下，优先选择带normal deal约束的bidder【例外，当deal的priority设置为same as buyer的时候则公平竞争】

在一次private auction的programmatic delivery过程中，DE只会给deal hitted的DSP发带normal deal的bid request，BidRequest中外部wseat=[]；而如果有guaranteed deal，则会为它另外新发一个bid request

guaranteed deal分为biddable和programmatic两种，若收到这样的bid response则一定会选择它。前者保证若DSP后方的buyer选择bid，那么这个bid response一定会被DSP返回给我们；后者在前者的基础上，保证DSP在某个比例上一定会返回bid response给我们。
```
---

## EPB Exclusive Programmatic Buyer
1. 痛点：Open RTB和Ad Served的Buyer都是需要事先由PX的sells团队与DSP或者Advertiser进行线下交流协商的，对于一些十分小的没能力进行线下交流的PX，往往会被DSP当作垃圾流量来处理。通过我们的EPB系统进行ad-setup-request的倒卖，这些小PX将不必与DSP进行直接交流。
2. EPB其实本质上就是一个PX，充当当前PX后边的reseller角色，卖长尾尾流量
3. EPB分为两种：
	1. SMI：只对主PX的ad-setup-request进行倒卖，赚取中介费，不需要保证买卖成功率
	2. Sticky FR：先对主PX的ad-setup-request进行打包的采买，再进行倒卖，需要保证买卖成功率来赚取利益
4. PX中若开放了EPB module（SMI-Sticky Management Inventory），那么PX中所有勾选了allowed EPB选项的Zone都将在EPB上创建唯一副本
5. EPB 默认处于buyer groups层面下的最低优先级
6. EPB 内部的bidder的优先级不是很重要，一般来说会将合规的bid response全部返回给publisher，以增加bid done几率
7. SMI计费包含两部分：
	1. SMI与PX商量好的分红比例 k%  ——> PX实际成交价 * k%收费
	2. SMI内部按照实际成交价收取的commission【为提高bid done几率，可将commission设为0%】




