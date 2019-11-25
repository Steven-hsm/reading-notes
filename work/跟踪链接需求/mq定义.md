## 1.  h5访问(小程序端)

1. 交换机名称: ex.h5.activity.visit

2. 队列名称:   queue.data.activity.date.visit.h5

3. 是否插入mq操作记录表:否

4. 参数说明:

   |     字段名      | 描述     |
   | :-------------: | -------- |
   |   sessionStr    | 子会话码 |
   | parentTopicType | 父级枚举 |
   |  parentTopicId  | 父级Id   |
   |    topicType    | 活动枚举 |
   |     topicId     | 活动id   |
   |     qrCode      | 渠道编码 |
   |      wuId       | 用户id   |
   |   channelType   | 渠道枚举 |
   |      ctime      | 访问时间 |

   

## 2. 取消支付(张杰)
1. 交换机名称: ex.order.pay.cancel

2. 队列名称:  queue.data.load.act.date.cancel.pay

3. 是否插入mq操作记录表:是

4. 参数说明:

   | 字段名  | 描述   |
   | :-----: | ------ |
   | orderId | 订单id |

## 3. 卡券过期(黄森明)

1. 交换机名称: ex.card.code.overdue

2. 队列名称:  queue.data.load.act.date.overdue.num

3. 是否插入mq操作记录表:否

4. 参数说明:
|  字段名   | 描述       |
| :-------: | ---------- |
| receiveId | 卡券領取id |
|  orderId  | 订单id     |

## 4. 红包过期(黄森明)
1. 交换机名称: ex.cash.voucher.expire

2. 队列名称:  queue.data.load.act.date.expire.red.pack

3. 是否插入mq操作记录表:否

4. 参数说明:
|  字段名   | 描述       |
| :-------: | ---------- |
| receiveId | 卡券領取id |
|  orderId  | 订单id     |

