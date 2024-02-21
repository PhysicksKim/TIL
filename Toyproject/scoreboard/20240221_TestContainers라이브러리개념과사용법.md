# TestContainers 라이브러리  
  
Test용 Docker 를 자동으로 띄워주는 라이브러리이다.  
docker hub 에 있는 이미지를 가져오고 실행하고 테스트 동안 사용한 다음 종료해주는 일련의 생명주기를 자동화 해준다.  
  
다만 TestContainers 사용 시 필수로 인지하고 있어야 할 사항들이 있는데  
Spring 의 Test 흐름도 잘 이해하고 있어야지 connection refused 같은 문제가 발생하지 않는다.  
  
<br><br>  

# TestContainers 사용 전 알아야 할 것들 
    
> Docker 에 대한 기본 개념은 필수  
> Docker 데몬도 실행중이여야 합니다      
   
1. TestContainers 는 RandomPort 로 DB 를 띄우기 때문에, 테스트 클래스마다 이를 캐치해서 Spring 에 db port 를 설정해줘야 한다.        
2. @SpringBootTest 로 띄워진 Test 용 Spring Context 는 다른 클래스에서도 재사용된다.     
3. (불확실) Spring Property 는 기본적으로 Spring 이 최초에 실행될 때에 정해지고 이후 변경이 불가하다.         
4. 다만, 테스트에서 동적으로 Spring Property 를 바꿀 수 있는 방법을 제공한다. static 메서드에 @DynamicPropertySource 를 사용해서 변경해야 한다.


<br><br>  

## 1. Random Port  
TestContainer 는 테스트간 독립성 및 충돌 방지를 위해 Random Port 로 Docker image 를 실행시킨다.  
따라서 서비스에서는 고정된 endpoint 로 연결되기 때문에 spring property 값에 곧바로 endpoint 를 적어주면 끝나지만  
Testcontainers 의 Image 는 Random Port 로 실행되기 때문에, Testcontianers Docker 실행 후 Spring Property 값을 변경시켜줘야 한다.  
  
<br><br>  

## 2. @SpringBootTest 의 재활용  
스프링은 테스트 속도 향상을 위해서 Spring Context 를 Clean 후 재사용 하는 방식으로 동작한다.  
즉 <code>PostServiceTest</code> 와 <code>MemberServiceTest</code> 두 테스트 클래스에서 모두 @SpringBootTest 를 사용한다면,  
각 테스트 클래스때마다 Spring 을 새로 실행시키는 게 아니라  
하나의 Spring Context 를 띄워두고 재사용 하도록 한다.  
  
<br><br>  

## 3. (불확실) Spring Property 는 최초 spring 시작 세팅 시 결정  
Spring Property 는 런타임에 변경되지 않고 최초 initialize 시점에 세팅된다  
따라서 TestContainers 가 Random Port 로 docker 를 실행시키기 때문에  
Random Port 맵핑을 위해서 추가적인 작업이 필요하다  

<br><br>  

## 4. @DynamicPropertySource  
Spring Boot Test 에서는 Property 를 java code 로 변경할 수 있도록 @DynamicPropertySource 를 제공한다.  
다만 @DynamicPropertySource 는 static 메서드에서만 사용해야 하므로 제약이 따른다.  
  
> 특히 static 시점은 AOP 로 처리할 수 없다는 단점이 있어서,
> Abstract Class 상속을 통해 코드 중복을 줄이는 방법을 택해야 한다.   
  
<br><br><br>  

---

<br><br><br>  

## 예시 Test Class  

```java
abstract public class AbstractRedisTestContainerInit {

    protected static final GenericContainer redisContainer;

    static {
        // Redis 컨테이너 초기화
        redisContainer = new GenericContainer(DockerImageName.parse("redis:7.0.15-alpine"))
                .withExposedPorts(6379);
        redisContainer.start();
    }

    // 스프링 테스트 환경에 동적으로 Redis 컨테이너의 포트 정보를 주입
    @DynamicPropertySource
    static void redisProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.data.redis.host", redisContainer::getHost);
        registry.add("spring.data.redis.port", () -> redisContainer.getMappedPort(6379));
        registry.add("spring.data.redis.password", () -> "1234");
    }
}
```
static 블럭에서 컨테이너를 초기화 후 실행하고  
@DynamicPropertySource 를 사용해서 spring data property 값을 변경해준다  

> @DynamicPropertySource 어노테이션은 소스코드의 주석에 "Testcontainers 를 위해" 설계된 어노테이션이라고 적혀있다.
> > This annotation and its supporting infrastructure were originally designed to allow properties from Testcontainers  based tests to be exposed easily to Spring integration tests.
  
```java

@Slf4j
@SpringBootTest
public class RedisContainerIntegrationTest extends AbstractRedisTestContainerInit {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Test
    void exampleTest() {
        Long add = stringRedisTemplate.opsForSet().add("testSet", "testValue");
        log.info("Add: {}", add);
    }
}
```

AbstractClass 로 Initalize 작업을 모두 공통 코드로 처리할 수 있어서  
위처럼 단순히 상속으로 Testcontainers init 작업을 해결할 수 있다.  
