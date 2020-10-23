# SE — Mongo Collections
code source：lite-stats/sr/tv/stickyads/mapstats/enums/mongo/Collection.java

1. ad_bidder_zone_blockrule_daily：通过blocklist被filter (“_id.bl”)
2. ad_bidder_zone_cowner_daily：content-owner (“_id.ow”)
3. ad_bidder_zone_daily：day上zone跟bidder传来的ad的相关统计数据 (“_id.d”)
4. ad_bidder_zone_hourly：hour时间尺度 (“_id.d“+“_id.h”)
5. ad_bidder_zone_minute：minute时间尺度（目前mongo中无数据）(“_id.d”+“_id.h”+“_id.m”)
6. ad_bidder_zone_second：second时间尺度（目前mongo中无数据）(“_id.d”+“_id.h”+“_id.m”+“_id.s”)
7. ad_bidder_zone_deal_daily：deal是offered—deal (“_id.di”)
8. ad_bidder_zone_domain_daily：domain可能是bid-response、click、impression等各个不同阶段的，不同阶段的统计数据也不同 (“_id.do”)
9. ad_bidder_zone_geo_daily【child topic，来源是AD_BIDDER_ZONE_GEO_DMA_DAILY】: country/region/dma code (“_id.co”+“_id.re”)
10. ad_geo_daily【child topic，来源是AD_BIDDER_ZONE_GEO_DMA_DAILY】：其实在code中是ad_bidder_geo_daily，country/region
11. zone_geo_daily【child topic，来源是AD_BIDDER_ZONE_GEO_DMA_DAILY】: country/region
12. ad_bidder_zone_os_device_browser_daily：操作系统、device、browser (“_id.os”+“_id.zd”+“_id.bw”)
13. ad_bidder_zone_source_value_daily：集中存储9种类型filter的统计数据，包含：
*AD_BIDDER_ZONE_SOURCE_AD_APPROVAL_DAILY*
*AD_BIDDER_ZONE_SOURCE_INTEGRATION_MRM_DAILY*
*AD_BIDDER_ZONE_SOURCE_SSL_DAILY*
*AD_BIDDER_ZONE_SOURCE_CB_SMP_DAILY*
*AD_BIDDER_ZONE_SOURCE_DURATION_DAILY*
*AD_BIDDER_ZONE_SOURCE_FLOORPRICE_DAILY*
*AD_BIDDER_ZONE_SOURCE_MULTIROLL_DAILY*
*AD_BIDDER_ZONE_SOURCE_CREATIVE_DAILY*
*AD_BIDDER_ZONE_SOURCE_AD_INCOMPATIBILITY_DAILY*
12. ad_bidder_zone_store_url_daily：AppStoreUrl
13. ad_bidder_zone_trackids_daily：custom-segments，用一个数组来存。custom-segments是在zone的rule中已经定好的，与ad是无关的，这里统计的就是ad命中了它bid的zone的这些custom-segments的一些数据 (“_id.ti”)
14. ad_daily/deal_daily/campaign_daily/objective_daily/zone_daily
15. visitor_unique_count：unique的visitor的计数
16. zone_integration_type_device_vpaid_daily【child topic，来源是AD_BIDDER_ZONE_INTEGRATION_TYPE_DEVICE_VPAID_DAILY
】：会包含一些gdpr、consent等统计信息(“_id.it”+“_id.dv”+“_id.vp”)