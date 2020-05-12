# 1. 定时任务

| 环境 | 地址                                       |
| ---- | ------------------------------------------ |
| 测试 | http://36d.kdwaimai.com/vip-core-task-api/ |
| 沙箱 | http://svr.kdwaimai.com/vip-core-task-api/ |
| 正式 |                                            |

## 1.1 获取任务列表(post)

地址:

> http://36d.kdwaimai.com/vip-core-task-api/trigger/list

入参:空

返回结果:

```json
{
    "state": true,
    "code": 0,
    "message": "成功",
    "desc": "37",
    "id": 0,
    "info": [
        {
            "triggerName": "channelWithdrawTrigger",
            "triggerGroup": "channelWithdrawTriggerGroup",
            "jobName": "channelWithdrawJob",
            "jobGroup": "channelWithdrawJobGroup",
            "jobDescription": null,
            "jobStatus": "NORMAL",
            "cronExpression": "0 */30 * * * ?",
            "createTime": "2020-01-08T03:01:12.000+0000",
            "previousFireTime": null,
            "nextFireTime": "2020-01-08T03:30:00.000+0000"
        }
    ],
    "name": null
}
```

## 1.2 执行任务(post)

地址:

> http://36d.kdwaimai.com/vip-core-task-api/job/execute

入参:(格式 body json)

```json
{
    "jobName": "secretExpireJob",
    "jobGroup": "secretExpireJobGroup"
}
```



| 参数     | 说明     |
| -------- | -------- |
| jobName  | 任务名称 |
| jobGroup | 任务组   |

任务说明:

| jobName                    | jobGroup                        | 描述                         |
| -------------------------- | ------------------------------- | ---------------------------- |
| channelWithdrawJob         | channelWithdrawJobGroup         | 渠道提现查询                 |
| travelCardOffJob           | travelCardOffJobGroup           | 自我游卡券下线查询           |
| cardPushToReshJob          | cardPushToReshJobGroup          | 推送卡券到工行               |
| cashVoucherExpireJob       | cashVoucherExpireJobGroup       | 红包券过期                   |
| vipCardSmsExpireJob        | vipCardSmsExpireJobGroup        | 会员到期短信提醒             |
| limitActMsgRemindJob       | limitActMsgRemindJobGroup       | 限时限量消息订阅             |
| cashVoucherCodeGenerateJob | cashVoucherCodeGenerateJobGroup | 现金红包 兑换码生成          |
| secretExpireJob            | secretExpireJobGroup            | 卡密过期任务                 |
| actResetBuyNumJob          | actResetBuyNumJobGroup          | 限量抢购每日凌晨限购数刷新   |
| openShopInfoSynJob         | openShopInfoSynJobGroup         | 同步店铺完整信息进入开放搜索 |
| actBaskStartJob            | actBaskStartJobGroup            | 晒单活动开始                 |
| redeemCodeGenerateJob      | redeemCodeGenerateJobGroup      | 兑换码自动生成               |
| vipCardExpireJob           | vipCardExpireJobGroup           | 会员到期提醒 定时任务        |
| shopWithdrawJob            | shopWithdrawJobGroup            | 店铺提现结果查询             |
| cardCodeExpireJob          | cardCodeExpireJobGroup          | 用户卡券过期提醒             |
| codeGenerateJob            | codeGenerateJobGroup            | 领取卡券码自动生成           |
| cashVoucherExpireNotifyJob | cashVoucherExpireNotifyJobGroup | 红包券过期定提醒时提醒任务   |
| msgFailTaskJob             | msgFailTaskJobGroup             | 自动扫描未发送的消息         |
| scanCardKeyGenerateJob     | scanCardKeyGenerateJobGroup     | 扫码领卡券兑换码自动生成     |
| openTravelSpecialJob       | openTravelSpecialJobGroup       | 同步自我游开放搜索           |
| shopCardExpireJob          | shopCardExpireJobGroup          | 门店卡券过期修改卡券状态     |
| cardCodeSmsExpireJob       | cardCodeSmsExpireJobGroup       | 卡券到期短信提醒             |
| actMsgRemindJob            | actMsgRemindJobGroup            | 限时限量活动消息订阅开始执行 |
| codeExpireRefundJob        | codeExpireRefundJobGroup        | 卡券过期自动退款             |
| injectUserJob              | injectUserJobGroup              | 霸王餐活动注入虚拟用户       |
| liquidationJob             | liquidationJobGroup             | 清算定时任务                 |
| secretGenerateJob          | secretGenerateJobGroup          | 秘钥生成                     |
| orderRefundJob             | orderRefundJobGroup             | 订单退款                     |
| activityUserRecordJob      | activityUserRecordJobGroup      | 清除昨天的活动用户记录数据   |
| openSynJob                 | openSynJobGroup                 | 同步访问信息进入开放搜索     |
| rfmStatisticsJob           | rfmStatisticsJobGroup           | RFM数据统计(用户管理)        |
| travelCardCostPriceJob     | travelCardCostPriceJobGroup     | 自我游成本价每月拉取         |
| openSearchJob              | openSearchJobGroup              | 同步开放搜索                 |
| scalesJob                  | scalesJobGroup                  | 调用体重秤                   |
| codeExpireJob              | codeExpireJobGroup              | 卡券过期定时任务             |
| channelUnlockFansJob       | channelUnlockFansJobGroup       | 渠道解粉定时任务             |
| mqExceptionJob             | mqExceptionJobGroup             | mq异常重发处理               |

# 2. mq实时触发

> mq的实时触发针对的是延迟队列,当然也可以针对处理异常的队列
>
> 使用条件:在mq_operate_record中存在记录

| 环境 | 地址                                       |
| ---- | ------------------------------------------ |
| 测试 | http://36d.kdwaimai.com/vip-core-stat-api/ |
| 沙箱 | http://svr.kdwaimai.com/vip-core-stat-api/ |
| 正式 |                                            |

## 2.1 实时发送

地址:

> http://36d.kdwaimai.com/vip-core-stat-api/mq/send/{id}

其中 id存放 mq_operate_record的主键,无需其他多余的入参