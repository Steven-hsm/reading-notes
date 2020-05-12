```sql
SELECT
a.sid channelCode,
c.channelName 渠道名称,
COUNT(DISTINCT a.id) 渠道新增用户,
SUM(d.buyCnt) 当月会员购买数,
SUM(d.tradeFunds) 当月会员购买金额
from
jkb_vip.user_into_log a
INNER JOIN (
	-- 获取第一次领取会员卡的数据
	SELECT min(id) id,wuId from jkb_vip.user_into_log a
	INNER JOIN jkb_vip.wx_union_user b on a.wxUnionId=b.wxUnionId
	GROUP BY b.wuId
) b on a.id = b.id
LEFT JOIN jkb_vip.channel_track_label c on a.sid=c.channelCode
-- 当月用户订单数据
LEFT JOIN (
	SELECT wuId,count(*) buyCnt,SUM(tradeFunds/100) tradeFunds from jkb_vip_core.orders
	where tradeFunds>0 and  payState=2 and FROM_UNIXTIME(ctime/1000,'%Y%m%d') BETWEEN 20190801 AND 20190831
GROUP BY wuId 
) d on b.wuId=d.wuId

where FROM_UNIXTIME(a.ctime/1000,'%Y%m%d') BETWEEN 20190801 AND 20190831
GROUP BY a.sid order by COUNT(DISTINCT a.id) desc,a.sid

```

