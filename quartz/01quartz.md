## quartz

[github地址]( https://github.com/Steven-hsm/quartz-task )

quartz调度一个任务的主要包括四个流程:

1. 继承Job类,实现execute方法,方法体就是任务执行的内容
2. 使用JobDetail包装job类(任务是有可能并发执行，如果Scheduler直接使用Job，就会存在对同一个Job实例并发访问的问题。而JobDetail & Job 方式，Sheduler每次执行，都会根据JobDetail创建一个新的Job实例，这样就可以规避并发访问的问题)
3. 实例化一个trigger类,用于配置触发任务的参数
4. 实例化调度器调度任务

下面是一个简单的demo:
```java
@Slf4j
public class SimpleJob implements Job {
    @Override
    public void execute(JobExecutionContext context) throws JobExecutionException {
        log.info("一个简单的任务正在执行中");
    }
}
```
```java
public class Test01SimpleJob {
    private static Scheduler scheduler;
    @Before
    public void init() throws Exception{
        //实例化调度器
        StdSchedulerFactory schedulerFactory = new StdSchedulerFactory();
        scheduler = schedulerFactory.getScheduler();
        //这里启动之后,调度器调度任务可以直接启动
        scheduler.start();
    }


    @Test
    public void simpleJob() throws Exception{
        // 构建Job信息
        JobDetail jobDetail = JobBuilder.newJob(SimpleJob.class)
            .withIdentity("任务名称", "任务组")
            .withDescription("这是任务描述")
            .build();

        //构建触发器
        Trigger trigger = TriggerBuilder.newTrigger()
            .withIdentity("触发器名称", "触发器组")
            .startNow()
            .build();

        //任务调度器调度任务
        scheduler.scheduleJob(jobDetail, trigger);
        //睡眠防止程序立马停止
        TimeUnit.MINUTES.sleep(10);
    }
}
```

### 1.  job相关说明

quartz 使用生成者模式构建代码,JobDetail,Trigger 分别使用JobBuilder,TriggerBuilder来创建.针对Job而言,源码可以主要看 JobBuilder 的build方法,可以看到这里面就是实例化一个JobDetail对象

```java
public JobDetail build() {
        JobDetailImpl job = new JobDetailImpl();
        
        job.setJobClass(jobClass);
        job.setDescription(description);
        if(key == null)
            key = new JobKey(Key.createUniqueName(null), null);
        job.setKey(key); 
        job.setDurability(durability);
        job.setRequestsRecovery(shouldRecover);
       
        if(!jobDataMap.isEmpty())
            job.setJobDataMap(jobDataMap);
        return job;
    }
```

在这里主要是对JobDetail的实现类进行实例化,JobBuilder在生成JobDetail时,就是在为其赋值.JobDetailImpl部分源码如下:

```java
public class JobDetailImpl implements Cloneable, Serializable, JobDetail {
    private static final long serialVersionUID = -6069784757781506897L;
    private String name;
    private String group;
    private String description;
    private Class<? extends Job> jobClass;
    private JobDataMap jobDataMap;
    private boolean durability;
    private boolean shouldRecover;
    private transient JobKey key;
```

使用JobBuilder构建JobDetail信息

```java
//获取需要插入的数据
JobDataMap jobDataMap = new JobDataMap();
jobDataMap.put("data", "需要使用的数据");

JobKey JobKey = new JobKey("任务名", "任务组");
// 构建Job信息
JobDetail jobDetail = JobBuilder.newJob(/*SimpleJobWithData.class 这里也可以这么写*/)
    //指定job任务类
    .ofType(SimpleJobWithData.class)
    //这里指定任务名称和任务组,方式有很多种
    .withIdentity(JobKey)
    //任务描述
    .withDescription("这是任务描述")
    //设置job的数据,可以在job的实现类中获取
    .setJobData(jobDataMap)
    //这里同上
    .usingJobData("data2", "第二种方式插入数据")
    .storeDurably(false)
    .requestRecovery(false)
    .build();
```

### 2. Trigger相关

​	Trigger也是使用TriggerBuilder构建,这里不贴源码.查看方式类似于Job.这里是初始化Trigger实例

```java
//构建触发器
TriggerKey triggerKey = new TriggerKey("触发器名称", "触发器组");
Trigger trigger = TriggerBuilder.newTrigger()
    .withIdentity(triggerKey)
    .withDescription("触发器描述")
    .startAt(new Date())
    .endAt(DateUtil.tomorrow())
    .withPriority(1)
    //.modifiedByCalendar("日程名称")
    .forJob(JobKey)
    .usingJobData("data3", "第二种方式插入数据")
    .withSchedule(null)
    .startNow()
    .build();
```

Trigger主要有两个实现类,SimpleTrigger,CronTrigger.

### 3.  Scheduler相关说明

Scheduler使用工厂模式构建,这里实例化一个调度器

```java
private static Scheduler scheduler;
@Before
public void init() throws Exception{
    StdSchedulerFactory schedulerFactory = new StdSchedulerFactory();
    scheduler = schedulerFactory.getScheduler();
    scheduler.start();
}
```

直接使用调度器调度任务和触发器

```java
//任务调度器调度任务
scheduler.scheduleJob(jobDetail, trigger);
```

调度完整的代码

#### 3.1 SimpleJobWithData.java

```java
@Slf4j
public class SimpleJobWithData implements Job {
    @Override
    public void execute(JobExecutionContext context) throws JobExecutionException {
        //获取传入的数据
        JobDataMap jobDataMap = context.getMergedJobDataMap();
        jobDataMap.forEach((key, value) -> {
            log.info("{}\t{}", key, value);
        });


    }
}
```

#### 3.2 Test05TriggerSchedule.java

```java
public class Test05TriggerSchedule {
    private static Scheduler scheduler;
    @Before
    public void init() throws Exception{
        StdSchedulerFactory schedulerFactory = new StdSchedulerFactory();
        scheduler = schedulerFactory.getScheduler();
        scheduler.start();
    }


    @Test
    public void withSchedule() throws Exception {

        //获取需要插入的数据
        JobDataMap jobDataMap = new JobDataMap();
        jobDataMap.put("data", "需要使用的数据");

        JobKey JobKey = new JobKey("任务名", "任务组");
        // 构建Job信息
        JobDetail jobDetail = JobBuilder.newJob()
                .ofType(SimpleJobWithData.class)
                .withIdentity(JobKey)
                .withDescription("这是任务描述")
                .setJobData(jobDataMap)
                .usingJobData("data2", "第二种方式插入数据")
                .storeDurably(false)
                .requestRecovery(false)
                .build();

        //构建触发器
        TriggerKey triggerKey = new TriggerKey("触发器名称", "触发器组");
        Trigger trigger = TriggerBuilder.newTrigger()
                .withIdentity(triggerKey)
                .withDescription("触发器描述")
                .startAt(new Date())
                .endAt(DateUtil.tomorrow())
                .withPriority(1)
                //.modifiedByCalendar("日程名称")
                .forJob(JobKey)
                .usingJobData("data3", "第二种方式插入数据")
                .withSchedule(
                        //设置调度的属性
                    SimpleScheduleBuilder.simpleSchedule()
                            .withIntervalInSeconds(1)
                            .withRepeatCount(3)
                )
                .startNow()
                .build();

        //任务调度器调度任务
        scheduler.scheduleJob(jobDetail, trigger);
        //睡眠防止程序立马停止
        TimeUnit.MINUTES.sleep(10);
    }
}
```

### 4. cron表达式说明

Cron表达式是一个字符串，一般由7个字符串组成.

* seconds minutes hours daysOfMonth months daysOfWeek years

这里需要注意以下事项

| 字符串      | 中文描述                | 格式           |                           |
| ----------- | ----------------------- | -------------- | ------------------------- |
| seconds     | 秒                      | , - * /        |                           |
| minutes     | 分                      | , - * /        |                           |
| hours       | 小时                    | , - * /        |                           |
| daysOfMonth | 月份的第几天 1-31       | ,- * ? / L W C | 不能和daysOfWeek同时存在  |
| months      | 月份 0-11               | , - * /        |                           |
| daysOfWeek  | 1-7 分别表示周日到周六  | - * ? / L C #  | 不能和daysOfMonth同时存在 |
| years       | 年 最大为当前年份+100年 | , - * /        |                           |

daysOfMonth和daysOfWeek比较特殊,两者不能同时存在,两者必续使用 ? 

```
* 表示匹配任意值
? daysOfMonth 和daysOfWeek 必须有一个为 ? 号,其他地方都不可用
- 表示范围如 1-7a/b 第a开始,没过b触发一次
, 表示多值 a,b 表示在第a和第b都执行
L 表示最后
W 表示有效工作日,只能用在daysOfMonthLW连用只能用在daysOfMonth,表示这个月份的最后一个工作日
# 只能用在 只能用在daysOfWeek 例如 4#2 表示某月的第二个周三
```

#### cron任务完整示例

```java
public class Test07CronTriggerSchedule {
    private static Scheduler scheduler;
    @Before
    public void init() throws Exception{
        StdSchedulerFactory schedulerFactory = new StdSchedulerFactory();
        scheduler = schedulerFactory.getScheduler();
        scheduler.start();
    }


    @Test
    public void withSchedule() throws Exception {

        //获取需要插入的数据
        JobDataMap jobDataMap = new JobDataMap();
        jobDataMap.put("data", "需要使用的数据");

        JobKey JobKey = new JobKey("任务名", "任务组");
        // 构建Job信息
        JobDetail jobDetail = JobBuilder.newJob()
                .ofType(SimpleJobWithData.class)
                .withIdentity(JobKey)
                .withDescription("这是任务描述")
                .setJobData(jobDataMap)
                .usingJobData("data2", "第二种方式插入数据")
                .storeDurably(false)
                .requestRecovery(false)
                .build();

        //构建触发器
        TriggerKey triggerKey = new TriggerKey("触发器名称", "触发器组");
        // Cron表达式调度构建器(即任务执行的时间)
        CronScheduleBuilder cron = CronScheduleBuilder.cronSchedule("*/5 * * * * ? 2020");
        Trigger trigger = TriggerBuilder.newTrigger()
                .withIdentity(triggerKey)
                .withDescription("触发器描述")
                .startAt(new Date())
                .endAt(DateUtil.tomorrow())
                .withPriority(1)
                //.modifiedByCalendar("日程名称")
                .forJob(JobKey)
                .usingJobData("data3", "第二种方式插入数据")
                .withSchedule(cron)
                .startNow()
                .build();

        //任务调度器调度任务
        scheduler.scheduleJob(jobDetail, trigger);
        //睡眠防止程序立马停止
        TimeUnit.MINUTES.sleep(10);
    }
}
```




















