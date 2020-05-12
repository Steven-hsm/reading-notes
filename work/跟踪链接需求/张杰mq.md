## 1. 取消支付(张杰)(新增)

1. 交换机名称: ex.order.pay.cancel

2. 队列名称:  queue.data.load.act.date.cancel.pay

3. 是否插入mq操作记录表:是

4. 参数说明:

   | 字段名  | 描述   |
   | :-----: | ------ |
   | orderId | 订单id |

## 2. 营销活动数据兑换数量(修改)

1. 交换机名称:ex.marketing.activity.redeem.cnt

2. 队列名称:   queue.data.load.market.act.date.redeem.cnt

3. 是否插入mq操作记录表:是

4. data 参数说明:

   |     字段名      | 描述       |
   | :-------------: | ---------- |
   |   sessionStr    | 子会话码   |
   | parentTopicType | 父级枚举   |
   |  parentTopicId  | 父级Id     |
   |    topicType    | 活动枚举   |
   |     topicId     | 营销活动id |
   |     qrCode      | 渠道编码   |
   |      wuId       | 用户id     |
   |   channelType   | 渠道枚举   |
   |      ctime      | 访问时间   |
   |   channelCode   | 渠道编码   |
   |    redeemCnt    | 兑换数量   |

## 3. 营销活动中奖次数(修改)

1. 交换机名称:ex.marketing.activity.redeem.cnt

2. 队列名称:   queue.data.load.market.act.date.redeem.cnt

3. 是否插入mq操作记录表:是

4. data 参数说明:

   |     字段名      | 描述       |
   | :-------------: | ---------- |
   |   sessionStr    | 子会话码   |
   | parentTopicType | 父级枚举   |
   |  parentTopicId  | 父级Id     |
   |    topicType    | 活动枚举   |
   |     topicId     | 营销活动id |
   |     qrCode      | 渠道编码   |
   |      wuId       | 用户id     |
   |   channelType   | 渠道枚举   |
   |      ctime      | 访问时间   |
   |   channelCode   | 渠道编码   |
   |     drawCnt     | 中奖次数   |