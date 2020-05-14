## spring框架整合quartz

### @Scheduled

spring下 spring-context-support有两个定时任务的注解,@EnableScheduling和@Scheduled,使用方式很简单

#### 1. spring可扫描到的地方增加EnableScheduling

```java
@SpringBootApplication
@MapperScan("com.hsm.quartztask.mapper")
@EnableScheduling
public class QuartzTaskApplication {
    public static void main(String[] args) {
        SpringApplication.run(QuartzTaskApplication.class, args);
    }
}
```

#### 2. 编写方法,在方法体上增加@Scheduled

```java
@Slf4j
@Component
public class ScheduledDemo {
    @Scheduled(cron = "*/5 * * * * ? ")
    void init() {
        log.info("spring 自己的简单定时任务");
    }
}
```

Scheduled属性详情可以查看以下源码

## Spring 整合quartz

[quartz介绍]("https://my.oschina.net/u/4160362/blog/4277640")

spring-boot-starter-quartz 项目源码中没有任何代码,在pom文件中可以看到

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
</dependency>
```

这里就直接使用了spring源码中的 spring-context-support项目.具体细节可以查看此项目org.springframework.scheduling.quartz包下的文件.

spring大量使用了工厂模式,而我们要做的就是实例化这些bean,源码可以主要查看以下文件:

* AdaptableJobFactory 这个方法可以增加一些对JobDateail的扩展
* QuartzJobBean 优化quartz的Job,将dataMap中的数据直接放到实例任务的属性中
* JobDetailFactoryBean JobDetai工厂,里面大部分属性也是JobDetail的属性
* SimpleTriggerFactoryBean SimpleTrigger工厂
* CronTriggerFactoryBean CronTrigger工厂
* SchedulerFactoryBean 调度器工厂

spring调度定时任务的步骤和quartz类似:

1. 编写任务类,可以继承Job,也可以继承QuartzJobBean (QuartzJobBean 继承自Job)
2. 实例化JobDetailFactoryBean ,将任务类与其绑定
3. 实例化TriggerFactoryBean,将JobDetail与之绑定
4. 实例化SchedulerFactoryBean ,将TriggerFactoryBean与之绑定

### 1. pom文件

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-quartz</artifactId>
</dependency>
```

### 2. 实例化工厂

```java
/**
 * QuartzJobBean 和Job的区别就是QuartzJobBean将job的datamap数据直接放到实例对象的属性中
 */
private String name;

@Override
protected void executeInternal(JobExecutionContext context) throws JobExecutionException {
    //TODO 发票查询
    log.info("从dataMap中获取的数据:{}", this.getName());
}
```

```java
@Bean("invoiceQueryJobDetail")
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public JobDetailFactoryBean jobDetailFactoryBean() {
    Map<String,Object> dataMap = new HashMap<>();
    dataMap.put("name", "开票查询");
    JobDetailFactoryBean jobDetailFactoryBean = new JobDetailFactoryBean();
    jobDetailFactoryBean.setName("任务名");
    jobDetailFactoryBean.setGroup("任务组");
    jobDetailFactoryBean.setJobClass(InvoiceQueryJobBean.class);
    jobDetailFactoryBean.setJobDataAsMap(dataMap);
    // 必须设置为true，如果为false，当没有活动的触发器与之关联时会在调度器中会删除该任务
    jobDetailFactoryBean.setDurability(true);
    return jobDetailFactoryBean;
}

@Autowired
private JobDetailFactoryBean invoiceQueryJobDetail;

@Bean("invoiceQueryTrigger")
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public CronTriggerFactoryBean cronTriggerFactoryBean() {
    CronTriggerFactoryBean cronTriggerFactoryBean = new CronTriggerFactoryBean();
    cronTriggerFactoryBean.setName("触发器名称");
    cronTriggerFactoryBean.setGroup("触发器组名称");
    cronTriggerFactoryBean.setJobDetail(invoiceQueryJobDetail.getObject());
    cronTriggerFactoryBean.setCronExpression("*/5 * * * * ?");
    return cronTriggerFactoryBean;
}

@Autowired
private QuartzJobFactory quartzJobFactory;
@Autowired
private CronTriggerFactoryBean invoiceQueryTrigger;

@Bean
public SchedulerFactoryBean scheduler(){
    SchedulerFactoryBean schedulerFactoryBean = new SchedulerFactoryBean();
    schedulerFactoryBean.setJobFactory(quartzJobFactory);
    schedulerFactoryBean.setTriggers(invoiceQueryTrigger.getObject());
    return schedulerFactoryBean;
}
```

具体工厂的属性可以自己尝试使用

### 3. 使用Spring Autowire

为了可以在Job类中直接只用Spring容器中的bean,这里实例化AdaptableJobFactory工厂

```java
@Component
public class QuartzJobFactory extends AdaptableJobFactory {
    @Autowired
    private AutowireCapableBeanFactory capableBeanFactory;
    @Override
    protected Object createJobInstance(TriggerFiredBundle bundle) throws Exception {
        Object jobInstance = super.createJobInstance(bundle);
        //这里将属性直接注入,那么就可以在job中使用spring中的bean了
        capableBeanFactory.autowireBean(jobInstance);
        return jobInstance;
    }
}
//将上面的bean当做SchedulerFactoryBean的一个属性注入
@Bean
public SchedulerFactoryBean scheduler(){
    SchedulerFactoryBean schedulerFactoryBean = new SchedulerFactoryBean();
    schedulerFactoryBean.setJobFactory(quartzJobFactory);
    schedulerFactoryBean.setTriggers(invoiceQueryTrigger.getObject());
    return schedulerFactoryBean;
}
```

### 4. 分享一个工具类

```java
@Slf4j
public final class QuartzUtils {
    private QuartzUtils() {
    }
    /**
     * @Author: huangsm on 2019/5/28 17:58
     * @param: [cronExpression] 0 0/5 * * * ?
     * @return: boolean
     * @Description: 检查cron表达式是否正确
     */
    public static boolean checkCron(String cronExpression){
        return CronExpression.isValidExpression(cronExpression);
    }
    /**
     * @Author: huangsm on 2019/5/28 18:46
     * @param: [cronExpression]
     * @return: void
     * @Description: 每隔五秒打印一次下次执行时间
     */
    public static void printExeTimeWith5Senconds(String cronExpression){
        CronExpression cron ;
        Date now = new Date();
        try {
            cron = new CronExpression(cronExpression);
            //上次执行时间
            Date lastDate = now;
            while(true){
                log.info("执行时间：{}", lastDate);
                lastDate = cron.getNextValidTimeAfter(lastDate);

                Thread.sleep(5000L);
            }
        } catch (ParseException e) {
            log.error("cron表达式解析异常", e);
        }catch (Exception e){
            log.error("打印cron表达式执行时间异常", e);
        }
    }

}
```

