

[TOC]

## EPB Mapping (no-deal)

### 需求

Admin level

1. All: Buyer Platform Type / Private Exchange / Private Exchange ID / Private Exchange Billing Country / Zone ID / DSP ID / DSP / Trading Desk / Trading Desk ID / Detected Device **[Available. Via collection px_smi_zone_dsp_td_buyer_dd_mv, with filter: selling_px_id is equal to 0]**
2. Deal only: Buyer Platform Type / Private Exchange / Private Exchange ID / Private Exchange Billing Country / Zone ID / DSP ID / DSP / Trading Desk / Trading Desk ID / Detected Device **[Almost available, we can only offer “Zone Device” as an alternative to “Detected Device”. Via collection px_smi_zone_dsp_td_buyer_deal_mv, with filter: selling_px_id is equal to 0]**

SMI level

1. All: Buyer Platform Type / Selling PX / Selling PX ID / Selling PX Billing Country / Zone ID / DSP ID / DSP / Trading Desk / Trading Desk ID / Detected Device **[Available. Via collection px_smi_zone_dsp_td_buyer_dd_mv, with filter: selling_px_id is not equal to 0]**
2. Deal only: Buyer Platform Type / Selling PX / Selling PX ID / Selling PX Billing Country / Zone ID / DSP ID / DSP / Trading Desk / Trading Desk ID / Detected Device **[Almost available, we can only offer “Zone Device” as an alternative to “Detected Device”. Via collection px_smi_zone_dsp_td_buyer_deal_mv, with filter: selling_px_id is not equal to 0]**

### 结果

**USE PX_SMI_ZONE_DSP_TD_BUYER_DD_MV_dist**



## EPB Mapping (deal)

### 需求

We have found a solution to provide both deal and detected device (support n2 and n4) via mongo collection: ea_seat_bpl_zone_deal_device_daily.

But this data is not in the database behind the looker, so I will investigate the data migration later

### 结果

**USE ea_seat_bpl_zone_deal_device_daily_dist** (join sticky_market_place_mapping, sticky_market_place)



### Domain EPB Mapping

### 需求

I am looking for a report of all domains on SMI per EPB Mapping PX for the last previous days

### 结果

**USE ad_bidder_zone_domain_country_os_device_daily_dist** (join sticky_market_place_mapping, sticky_market_place)

https://looker.stickyadstv.com/looks/70