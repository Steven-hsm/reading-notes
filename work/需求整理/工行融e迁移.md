1. 工行上线活动推送活动

   jkb-task com.doyd.service.impl.ICBCActivityService#pushOnlineAct 清缓存处理

2. 卡券推送任务

   jkb-task com.doyd.task.job.cardcode.CardPushToRESHJob#execute

3. 卡券过期工行退款(调用接口)

   jkb-task com.doyd.service.impl.CardCodeTaskService#codeExpireRefund

4. 卡券领取推送工行消息(祖高)

   jkb-external 

5. 卡券过期推送工行消息

   jkb-task com.doyd.service.impl.CardQueueTaskService#cardOffDeal

