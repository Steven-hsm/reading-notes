## 增加sql

```sql
-- 活动承载页数据统计
ALTER TABLE `jkb_vip_core`.`data_load_act_date` 
ADD COLUMN `alreadyRecycleCnt` int(11) NOT NULL DEFAULT 0 COMMENT '已回收数量' AFTER `joinLotteryPeopleByShare`,
ADD COLUMN `alreadyExchange` int(11) NOT NULL DEFAULT 0 COMMENT '已兑换数量' AFTER `alreadeRecycleCnt`;
```





## 1 已回收数量统计

### mq发送条件

> 后台上传券码回收卡券时发送

### mq说明

| 注意事项                                | 说明                                          |
| --------------------------------------- | --------------------------------------------- |
| 交换机(exchange)                        | ex.act.card.recycle                           |
| 队列(queue)                             | queue.data.activity.date.card.recycle         |
| 路由键(routeKey)                        | 扇形交换机(fanout,这个参数是否填写不影响结果) |
| 队列类型(是否延迟队列)                  | 非延迟队列                                    |
| 是否插入mq操作记录表(mq_operate_record) | 否                                            |
### 参数说明

| 字段名称   | 字段类型 | 是否可为空 | 说明     | 关联表   |
| ---------- | -------- | ---------- | -------- | -------- |
| actId      | int      | 否         | 活动id   | activity |
| recycleNum | int      | 否         | 回收数量 |          |
| ctime      | long     | 否         | 回收时间 |          |

## 2已兑换数量

### mq发送条件

> 活动兑换时发送

### mq说明

| 注意事项                                | 说明                                          |
| --------------------------------------- | --------------------------------------------- |
| 交换机(exchange)                        | ex.act.card.redeem                            |
| 队列(queue)                             | queue.data.activity.date.act.redeem           |
| 路由键(routeKey)                        | 扇形交换机(fanout,这个参数是否填写不影响结果) |
| 队列类型(是否延迟队列)                  | 非延迟队列                                    |
| 是否插入mq操作记录表(mq_operate_record) | 否                                            |
### 参数说明

| 字段名称  | 字段类型 | 是否可为空 | 说明     | 关联表   |
| --------- | -------- | ---------- | -------- | -------- |
| actId     | int      | 否         | 活动id   | activity |
| redeemNum | int      | 否         | 兑换数量 |          |
| ctime     | long     | 否         | 兑换时间 |          |