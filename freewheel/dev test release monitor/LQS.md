

**查询scribe log**

```sql
SELECT bidderid, responsecontent FROM glue_sfx.temp."br" where responsestatus='RECEIVED' and bidderid in (SELECT id from mysql_sfx.stickyads."bidder" where buyer_platform_id=485) LIMIT 10000
```

**查询mongo**

```sql
SELECT pdate, sum(value_ct) as ct, sum(value_ctb) as ctb FROM db.sfx_aggregate."ad_bidder_zone_daily" where pdate>=20210228 group by pdate order by pdate LIMIT 100
```

**通过viewersessionid拉取log，聚合查询selling-px和epb的相连的#bs**

```sql
select a.viewersessionid, a.zoneid,b.zoneid, a.publisherwinprice, b.publisherwinprice,a.advertiserwinprice, b.advertiserwinprice from glue_sfx.temp.bs a join glue_sfx.temp.bs b
on a.viewerid = b.viewerid and a.viewersessionid = b.viewersessionid
and a.zoneid <> b.zoneid 
and a.batch = 2021040806 and b.batch = 2021040806

and b.bidderid  in (1206658, 1633969, 1661969, 1678289) 
where a.publisherwinprice <> b.publisherwinprice
limit 10000
```

**EPB margin查询验证**

```sql
SELECT id_b, id_bri, id_z, sum(value_ct) as ct, sum(value_rv) as rv, sum(value_upct) as upct, sum(value_ctb) as ctb, sum(value_rvb) as rvb, sum(coalesce(value_rv,0))+sum(coalesce(value_rvb,0))-sum(coalesce(value_ctb,0))-sum(coalesce(value_upct,0)) as margin FROM db.sfx_aggregate."ad_bidder_zone_daily" where pdate=20210427 and id_z in (select zoneid from mysql_sfx.stickyads.zones where affiliateid in (select affiliateid from mysql_sfx.stickyads.affiliates where agencyid=123)) and id_b is not null group by (id_b, id_bri, id_z) LIMIT 1000

SELECT id_d, id_b, id_bri, id_z, value_ct as ct, value_rv as rv, value_upct as upct, coalesce(value_rv,0)-coalesce(value_upct,0) as margin FROM db.sfx_aggregate."ad_bidder_zone_daily" where pdate=20210427 and id_z in (select zoneid from mysql_sfx.stickyads.zones where affiliateid in (select affiliateid from mysql_sfx.stickyads.affiliates where agencyid=123)) and id_b is not null and value_upct is not null LIMIT 1000
```

