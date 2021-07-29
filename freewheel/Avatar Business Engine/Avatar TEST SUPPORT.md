[TOC]



## ExternalAd

```
prepareRawRealExtAdMsg
```

每隔一段时间，随机从mysql中选取limit个真实存在的banner，获取其externalref对应的externalAd的info、以及externalref对应的bidderId，由此fake一批test externalAd，发送到staging kafka中供ExternalAdHandler整体流程测试



## Seat

每隔一段时间，随机从mysql中选取limit个真实存在的seat，获取其buyerPlatformId，一起发送到staging kafka供SeatHandler整体流程测试



## Campaign

每隔一段时间，随机从mysql中选取limit个真实存在的campaign，发送campaignId到staging kafka中供CampaignHandler整体流程测试



**⚠️ Seat和Campaign的测试也可省略，由Staging Mapstats触发即可。只需保证单测逻辑正确**



