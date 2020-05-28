说明:

> 1. 延迟队列的发送对象:mq_operate_record的主键
> 2. 延迟队列超过天数限制也正常发送,消费端做相关处理

## 1. banner有效期开始

### mq发送条件

> 对存在有效期限制的banner做新增或者修改有效期开始时间操作时

### mq说明

| 注意事项                                | 说明                                                      |
| --------------------------------------- | --------------------------------------------------------- |
| 交换机(exchange)                        | ex.banner.time.start.delay                                |
| 队列(queue)                             | queue.banner.time.start.delay                             |
| 路由键(routeKey)                        | 扇形交换机(fanout,这个参数是否填写不影响结果)             |
| 队列类型(是否延迟队列)                  | 是,发送时取mq_operate_record的主键,表中data字段为参数说明 |
| 是否插入mq操作记录表(mq_operate_record) | 是                                                        |
### 参数说明

| 字段名称 | 字段类型 | 是否可为空 | 说明       | 关联表       |
| -------- | -------- | ---------- | ---------- | ------------ |
| bannerId | int      | 否         | banner主键 | index_banner |

### 备注

> mq_operate_record 的startDealTime存index_banner表的restStartTime

## 1. banner有效期截止

### mq发送条件

> 对存在有效期限制的banner做新增或者修改有效期结束时间操作时

### mq说明

| 注意事项                                | 说明                                                      |
| --------------------------------------- | --------------------------------------------------------- |
| 交换机(exchange)                        | ex.banner.time.end.delay                                  |
| 队列(queue)                             | queue.banner.time.end.delay                               |
| 路由键(routeKey)                        | 扇形交换机(fanout,这个参数是否填写不影响结果)             |
| 队列类型(是否延迟队列)                  | 是,发送时取mq_operate_record的主键,表中data字段为参数说明 |
| 是否插入mq操作记录表(mq_operate_record) | 是                                                        |

### 参数说明

| 字段名称 | 字段类型 | 是否可为空 | 说明       | 关联表       |
| -------- | -------- | ---------- | ---------- | ------------ |
| bannerId | int      | 否         | banner主键 | index_banner |

### 备注

> mq_operate_record 的startDealTime存index_banner表的restEndTime

