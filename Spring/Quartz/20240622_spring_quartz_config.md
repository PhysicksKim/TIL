# Spring Bean 주입을 위한 config 
  
기본적으로 Spring boot starter Quartz 가 있는데     
Quartz Job 이 의존성 주입을 사용하도록 하기 위해서 설정을 좀 해줘야 한다.  
   
### Job 예시    
  
```java
@Slf4j
@RequiredArgsConstructor
@Component
public class StartLineupJob implements Job {

    private final StartLineupService startLineupService;

    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        log.info("StartLineupJob executed at {}", LocalDateTime.now());
        startLineupService.getStartLineup();
    }
}
```
  
위 코드는 Quartz Job 을 구현하면서,  
Service 가 <code>private final</code>로 생성자 주입을 받을 수 있도록 해뒀다.  
  
그런데 별다른 설정을 해주지 않은채 위 job 을 사용한다면  
bean 주입이 안돼서 예외가 발생한다.  
  
```
@Slf4j
@RequiredArgsConstructor
@Service
public class SchedulerService {

    private final Scheduler scheduler;

    public void start() {
        try {
    // 여기서 Job 을 생성해주게 되는데, Job instance 생성 시 bean 주입에 실패한다  
            JobDetail jobDetail = JobBuilder.newJob(StartLineupJob.class)
                    .withIdentity("beanTest", "group2")
                    .build();
            Trigger trigger = TriggerBuilder.newTrigger()
                    .withIdentity("startLineupTrigger", "group2")
                    .startAt(Date.from(Instant.now().plus(5, ChronoUnit.SECONDS)))
                    .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                            .withIntervalInSeconds(5)
                            .repeatForever())
                    .build();

            scheduler.scheduleJob(jobDetail, trigger);

            log.info("스케쥴러 시작 , 5초 후 StartLineupJob 실행 , 5초 간격으로 실행, 최초 실행 {} ", LocalDateTime.now().plusSeconds(5));
        } catch (SchedulerException e) {
            e.printStackTrace();
        }
    }
}
```
  
test 코드로 간단히 위 SchedulerService.start() 를 실행 해주면 에러가 발생한다  
  
### 테스트 코드  
  
```java
@Test
public void testBean() throws Exception {
    schedulerService.beanTest();
    Thread.sleep(15000); // 30초 동안 대기하며 StartLineupJob의 실행을 확인합니다.
}
```

### 에러 로그  
  
```java
2024-06-22T22:42:22.992+09:00  INFO 24104 --- [    Test worker] c.g.s.domain.quartz.SchedulerService     : 스케쥴러 시작 , 5초 후 StartLineupJob 실행 , 5초 간격으로 실행, 최초 실행 2024-06-22T22:42:27.991591 
2024-06-22T22:42:27.971+09:00 ERROR 24104 --- [SchedulerThread] org.quartz.core.ErrorLogger              : An error occured instantiating job to be executed. job= 'group2.beanTest'

org.quartz.SchedulerException: Job instantiation failed
	at org.springframework.scheduling.quartz.AdaptableJobFactory.newJob(AdaptableJobFactory.java:47) ~[spring-context-support-6.1.3.jar:6.1.3]
	at org.quartz.core.JobRunShell.initialize(JobRunShell.java:127) ~[quartz-2.3.2.jar:na]
	at org.quartz.core.QuartzSchedulerThread.run(QuartzSchedulerThread.java:392) ~[quartz-2.3.2.jar:na]
Caused by: java.lang.NoSuchMethodException: com.gyechunsik.scoreboard.domain.football.scheduler.StartLineupJob.<init>()
	at java.base/java.lang.Class.getConstructor0(Class.java:3585) ~[na:na]
	at java.base/java.lang.Class.getDeclaredConstructor(Class.java:2754) ~[na:na]
	at org.springframework.util.ReflectionUtils.accessibleConstructor(ReflectionUtils.java:185) ~[spring-core-6.1.3.jar:6.1.3]
	at org.springframework.scheduling.quartz.AdaptableJobFactory.createJobInstance(AdaptableJobFactory.java:61) ~[spring-context-support-6.1.3.jar:6.1.3]
	at org.springframework.scheduling.quartz.AdaptableJobFactory.newJob(AdaptableJobFactory.java:43) ~[spring-context-support-6.1.3.jar:6.1.3]
	... 2 common frames omitted

2024-06-22T22:42:27.984+09:00  INFO 24104 --- [SchedulerThread] o.s.s.quartz.LocalDataSourceJobStore     : All triggers of Job group2.startLineupTrigger set to ERROR state.
```

로그에서 보다시피 Job instantiation 과정에서 실패했다는 메세지다.  
이를 해결하기 위해 Spring Bean 주입이 가능한 Job Factory 를 설정해줘야 한다.  
  
<br><br>  
  
# SpringBeanJobFactory Bean 추가
  
```java
@Configuration
public class SpringBeanJobFactoryConfig {

    @Bean
    public SpringBeanJobFactory springBeanJobFactory() {
        return new SpringBeanJobFactory();
    }

}
```

```java
@Configuration
public class QuartzConfig {

    @Autowired
    private SpringBeanJobFactory springJobFactory;

    @Bean
    public SchedulerFactoryBean schedulerFactoryBean() {
        SchedulerFactoryBean factory = new SchedulerFactoryBean();
        factory.setJobFactory(springJobFactory);
        return factory;
    }

}
```
  
위와 같이 Config 를 통해서 Job Factory 로 SpringBeanJobFactory 를 사용해주면   
Job 에 Bean 주입 문제가 해결된다.  
  
SpringBeanJobFactory 는 앞서 본 Job 생성 시 Bean 주입 문제를 해결해준다.  
Quartz 자체는 Spring과 무관한 별개 라이브러리 이므로,  
Spring 측에서 Quartz Job 에 대한 Bean 주입 문제를 해결해주기 위해서  
SpringBeanJobFactory 를 구현해 두었다.  
  
### SpringBeanJobFactory 의 createJobInstance() 구현    
SpringBeanJobFactory 를 보면  

```
public class SpringBeanJobFactory extends AdaptableJobFactory
		implements ApplicationContextAware, SchedulerContextAware {

  @Nullable
  private ApplicationContext applicationContext;
```
Bean 주입을 위해 <code>ApplicationContext</code> 를 필드에 두고  
  
```
@Override
	protected Object createJobInstance(TriggerFiredBundle bundle) throws Exception {
		Object job = (this.applicationContext != null ?
				this.applicationContext.getAutowireCapableBeanFactory().createBean(bundle.getJobDetail().getJobClass()) :
				super.createJobInstance(bundle));
```

<code>createJobInstance()</code> 메서드에서 위와 같이 applicationContext 를 사용해서 bean 을 주입한 job 을 생성하는 것을 볼 수 있다.  
  
