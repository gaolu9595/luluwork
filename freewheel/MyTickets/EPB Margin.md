

# EPB Margin Calculation

[TOC]

## Schedule

1. 04-14第一次release：
   1. SE：把“upct”数修好【观察看看数准不准确】
2. 第二次release：
   1. SE：新增一个字段“ctm”，存放ct和upct之间的有效值【观察看看数准不准确】
   2. SE：需要加一个时间开关来保证cut over那几天不写”ctm“字段，在一个统一的零点开始让”ctm“成为唯一的有效值
   3. UI：修改“retry”的逻辑【需要保证ctm存放的是有效值，在没有“ctm”之前不更新“ctm”】
3. 第三次release：
   1. UI：修改UI展示逻辑【在此之前需要保证ctm的数是准确的！！！】



## Features

Fw-45459 新增upct

Fw-54911 修upct

Fw-55000 新增ctm

<img src="/Users/lugao/Library/Application Support/typora-user-images/image-20210420174520431.png" alt="image-20210420174520431" style="zoom:50%;" />

1. related valorizer Redstats:

   ```
   AdBidderZoneDailyKey_valorizer[0-6].conf
   AdBidderZoneDomainCountryOsDeviceBrowserHourlyKey_valorizer[0-13].conf
   AdBidderZoneTrackIdsDailyKey_valorizer[0-6].conf
   AdBidderZoneGeoDMADailyKey_valorizer[0-14].conf
   AdBidderZoneHourlyKey_valorizer[0-6].conf
   AdBidderZoneOsDeviceBrowserDailyKey_valorizer[0-7].conf
   AdBidderZoneContentOwnerDailyKey_valorizer[0-8].conf
   AdBidderZoneDomainCountryOsDeviceDailyKey_valorizer[0-29].conf
   AdBidderZoneDealDailyKey_valorizer[0-3].conf
   AdBidderZoneDomainDailyKey_valorizer[0-15].conf
   AdBidderZoneStoreUrlDailyKey_valorizer[0-7].conf
   AdBidderZoneIntegrationTypeDeviceVpaidDailyKey_valorizer[0-1].conf
   AdBidderZoneAppBundleIdDailyKey_valorizer[0-5].conf
   ```

   

2. related collections:

```
AdBidderZoneDailyKey
	AgencyZoneTypeZoneDeviceDailyKey
	BidderPublisherZoneTypeZoneDeviceDailyKey
	CampaignDailyKey
	PublisherZoneTypeZoneDeviceDailyKey
	SiteBidderCampaignZoneTypeZoneDeviceDailyKey
	ZoneDailyKey
AdBidderZoneDomainCountryOsDeviceBrowserHourlyKey
AdBidderZoneTrackIdsDailyKey
AdBidderZoneGeoDMADailyKey
	AgencyZIntegrationTypeZTypeZDeviceZSkipZSSLZVpaidGeoDMADailyKey
	SiteGeoZoneTypeZoneDeviceDailyKey
	ZoneGeoDailyKey
	AdBidderGeoDailyKey
	AdBidderZoneGeoDailyKey
	AgencyGeoZoneTypeZoneDeviceDailyKey
AdBidderZoneHourlyKey
AdBidderZoneOsDeviceBrowserDailyKey
AdBidderZoneContentOwnerDailyKey
AdBidderZoneDomainCountryOsDeviceDailyKey
AdBidderZoneDealDailyKey
	BidderPublisherDealDailyKey
AdBidderZoneDomainDailyKey
	AgencyZIntegrationTypeZTypeZDeviceZSkipZSSLZVpaidDomainDailyKey
	AgencyZIntegrationTypeZTypeZDeviceZSkipZSSLZVpaidTopDomainDailyKey
	SiteDomainZoneTypeZoneDeviceDailyKey
AdBidderZoneStoreUrlDailyKey
AdBidderZoneIntegrationTypeDeviceVpaidDailyKey
	ZoneIntegrationTypeDeviceVpaidDailyKey
AdBidderZoneAppBundleIdDailyKey
```

```
dbbackup@nycsspbackup01.fwmrm.net:/var/backups/mongodb/daily · 03:30 AM Fri May 21 ·
!5008 $ du -sh */*|grep 20210517
👉5.3G	ad_bidder_zone_app_bundle_id_daily/20210517
23M	ad_bidder_zone_blockrule_daily/20210517
👉8.1G	ad_bidder_zone_cowner_daily/20210517
👉4.5G	ad_bidder_zone_daily/20210517
1.2G	ad_bidder_zone_deal_daily/20210517
👉16G	ad_bidder_zone_domain_daily/20210517
512	ad_bidder_zone_domain_daily_sample/20210517
👉28G	ad_bidder_zone_geo_daily/20210517
1.0K	ad_bidder_zone_geo_daily_sample/20210517
👉24G	ad_bidder_zone_hourly/20210517
1.0K	ad_bidder_zone_hourly_sample/20210517
512	ad_bidder_zone_minute/20210517
👉11G	ad_bidder_zone_os_device_browser_daily/20210517
512	ad_bidder_zone_os_device_browser_daily_sample/20210517
512	ad_bidder_zone_second/20210517
131M	ad_bidder_zone_source_value_daily/20210517
👉4.7G	ad_bidder_zone_store_url_daily/20210517
👉2.2G	ad_bidder_zone_trackids_daily/20210517
178K	ad_daily/20210517
👉2.7G	ad_geo_daily/20210517
3.3M	agency_dmp_segments_daily/20210517
512	agency_dmp_segments_health/20210517
👉117M	agency_geo_zt_zd_daily/20210517
👉262M	agency_zit_zt_zd_zsk_zsl_zvp_domain_daily/20210517
👉99M	agency_zit_zt_zd_zsk_zsl_zvp_geo_dma_daily/20210517
👉115M	agency_zit_zt_zd_zsk_zsl_zvp_top_domains_daily/20210517
👉105M	agency_zit_zt_zd_zsk_zsl_zvp_top_domains_stream_daily/20210517
👉2.0M	agency_zt_zd_daily/20210517
512	auction_simulator/20210517
512	batches_done/20210517
54M	bidder_publisher_deal_daily/20210517
👉196M	bidder_publisher_zt_zd_daily/20210517
8.2G	bidder_zone_auction_type_geo_daily/20210517
2.5G	bidder_zone_bid_original_price_priority_daily/20210517
2.4G	bidder_zone_bidprice_daily/20210517
512	blocklist_summary/20210517
512	bpl_country_daily/20210517
1.2M	bpl_zd_device_daily/20210517
21M	bpl_zd_device_hourly/20210517
👉701M	campaign_daily/20210517
247M	clickhouse_visitor_unique_count/20210517
1.7M	deal_daily/20210517
137M	ea_bpl_seat_daily/20210517
2.8G	ea_seat_bpl_site_zd_device_daily/20210517
3.3G	ea_seat_bpl_zone_deal_device_daily/20210517
512	ea_seat_bpl_zone_deal_device_daily_sample/20210517
305K	last_update/20210517
512	litestats_run/20210517
512	mapred_queue/20210517
9.5K	objective_daily/20210517
👉12M	publisher_zt_zd_daily/20210517
512	recalc_queue/20210517
512	redstats_run/20210517
512	running_queries/20210517
512	seat_bpl_zd_device_country_daily/20210517
👉7.0G	site_bidder_campaign_zt_zd_daily/20210517
👉226M	site_domain_zt_zd_daily/20210517
👉1.1G	site_geo_zt_zd_daily/20210517
512	tmp_bidder_publisher_deal_daily/20210517
8.0M	topk_agency_zit_zt_zd_zsk_zsl_zvp_top_domains_stream_daily/20210517
512	unique_id_caches.chunks/20210517
512	unique_id_caches.files/20210517
512	unique_id_caches_ny5.chunks/20210517
512	unique_id_caches_ny5.files/20210517
258M	visitor_unique_count/20210517
👉33M	zone_daily/20210517
👉1.1G	zone_geo_daily/20210517
👉110M	zone_integration_type_device_vpaid_daily/20210517
```

operations:

remove "value.ctm", "value.a.ctm", "value.s.ctm" from 2021.05.25-2021.05.29 in above collection

