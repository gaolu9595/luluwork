## Update

1. refactor views model (整理kpi，名字铺平等)
2. 创建新的物化视图满足BI：

```
Admin level
All: Buyer Platform Type / Private Exchange / Private Exchange ID / Private Exchange Billing Country / Zone ID / DSP ID / DSP / Trading Desk / Trading Desk ID / Detected Device [Available, via collection ad_bidder_zone_domain_country_os_device]
Deal only: Buyer Platform Type / Private Exchange / Private Exchange ID / Private Exchange Billing Country / Zone ID / DSP ID / DSP / Trading Desk / Trading Desk ID / Detected Device[Available, via collection bidder_zone_auctiontype_geo]
 
SMI level
All: Buyer Platform Type / Selling PX / Selling PX ID / Selling PX Billing Country / Zone ID / DSP ID / DSP / Trading Desk / Trading Desk ID / Detected Device
[not available, but doable via collection ad_bidder_zone_domain_country_os_device, need to do some mapping]
 
Deal only: Buyer Platform Type / Selling PX / Selling PX ID / Selling PX Billing Country / Zone ID / DSP ID / DSP / Trading Desk / Trading Desk ID / Detected Device[not available, doable via collection bidder_zone_auctiontype_geo, need to do some mapping]
 
```

3. file in : ~/work/looker/

4. 踩坑：

   ```json
   Code: 349. DB::Exception: Received from localhost:9000. DB::Exception: Cannot convert NULL value to non-Nullable type: while converting source column zone_device to destination column zone_device: while executing 'FUNCTION CAST(zone_device :: 9, Int32 :: 48) -> CAST(zone_device, Int32) Int32 : 47'.
   [更改字段的类型，使其nullable，例如`selling_px_zone_id` Nullable(Int32)]
   ```

   ```
   【looker查询时，measure中number和sum的区别有可能会报错，设为number的话需要在group by中出现】
   ClickHouse exception, code: 215, host: 172.16.31.1, port: 8123; Code: 215, e.displayText() = DB::Exception: Column `vtr0` is not under aggregate function and not in GROUP BY: While processing ad AS ........
   ```

Q: 

1. rwo要不要加在clickhouse里(目前新创建的两个物化视图都没有这个数据)
2. rdb、bid_filtered、response_with_ad做二次计算
3. unk.viewability = impression - viewability impression - non viewability impression 
4. 

