## 1. 卡券过期(黄森明)(新增)

1. 交换机名称: ex.card.code.overdue

2. 队列名称:  queue.data.load.act.date.overdue.num

3. 是否插入mq操作记录表:否

4. 参数说明:

|  字段名   | 描述       |
| :-------: | ---------- |
| receiveId | 卡券領取id |
|  orderId  | 订单id     |

## 2. 红包过期(黄森明)(新增)

1. 交换机名称: ex.cash.voucher.expire

2. 队列名称:  queue.data.load.act.date.expire.red.pack

3. 是否插入mq操作记录表:否

4. 参数说明:

|  字段名   | 描述       |
| :-------: | ---------- |
| receiveId | 卡券領取id |
|  orderId  | 订单id     |

