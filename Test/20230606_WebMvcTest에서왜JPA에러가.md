# 에러 발생
  
Controller Test에서 Mock으로 Service를 채워넣으려고 했는데  
이상하게 JPA metamodel 이 비어있다는 에러를 봤다.  
  
```
Caused by: java.lang.IllegalArgumentException: JPA metamodel must not be empty!
```
  
MockBean 이라면 JPA랑 상관 없을텐데 왜 JPA 에러가 발생하지?  
혹시 Service가 Repository 의존한다고 Mock만들다가
Repository를 갖고 와버리나? 같은 이상한 생각까지 해봤다.    
  
### 선 결론  
결론 @WebMvcTest는 @SpringBootApplication가 붙은 SpringBoot Main 진입점을 사용하는데  
여기에 JPA 관련 코드가 있으면 JPA 를 활성화 하려고 빈들을 찾게 된다.    
나의 경우 Auditing 관련 JPA Annotation 이 같이 있었다.  
  
```java
@SpringBootApplication
@EnableJpaAuditing
public class SecondBoardApplication { ...
```
   
[해결 방안](https://januaryman.tistory.com/m/519)  
그래서 이걸 따로 분리해준다거나, Test에서 그냥 JPA Mock을 집어넣거나, 그냥 아예 SpringBootTest 해버리는 3가지 해결책이 있다.  

나는 Test 에서 JPA Mock을 집어넣어 주도록 했다.  

### 해결 부분 코드  
```java
@WebMvcTest(BoardController.class) 
@MockBean(JpaMetamodelMappingContext.class)
class BoardControllerWebMvcTest {
```  

JpaMetamodel Mock을 집어넣어줘서 해결  

<br><br><br>  

---

<br><br><br>

# 기록용    
  
### 원인  
[출처 - Getting "At least one JPA metamodel must be present" with @WebMvcTest](https://stackoverflow.com/questions/41250177/getting-at-least-one-jpa-metamodel-must-be-present-with-webmvctest)  
  
따로 Configuration 파일을 빼주면 WebMvcTest가 테스트시에 @Configuration 은 제외하고 필요한 Spring만 이용한다. (아마도?)   
그런데 @SpringBootApplication 아래에 @EntityScan 같은 JPA 관련 어노테이션이 붙으면  
Spring 진입점을 사용하다가 JPA 까지 같이 로딩해버려서 문제가 생긴다고 한다.  
  
### 해결 방안
  
나는 위에서 제시한 3가지 방법 중 Test에 JPA MOCK 을 집어 넣는 방식을 사용했다.  
선 결론 부분에 포함된 링크에서 Config를 따로 분리하는 방법은  
나중에 JPA 가 필요한 테스트에서 또 다시 JPA가 또 로드 안되는 문제가 있다고 하니까  
그냥 WebMvcTest 시에만 JPAMock을 넣어주도록 하는게 나을 것 같아서  
WebMvcTest 마다 @MockBean(JpaMetamodelMappingContext.class) 이걸 넣어주기로 결정했다.  
  
# 에러 발생시 코드  
  
### Test 

```java
@WebMvcTest(BoardController.class) 
class BoardControllerWebMvcTest {

  private static String BOARD_MAIN_URL = "/board";
  private static String BOARD_MAIN_PAGE = "board";

  @Autowired
  private MockMvc mockMvc;

  @MockBean
  private BoardService boardService;

  @Test
  void mainPage() throws Exception{
    mockMvc.perform(get(BOARD_MAIN_URL))
            .andExpect(status().isOk())
            .andExpect(view().name(BOARD_MAIN_PAGE));
  }
  ...
}
```
  
### 에러 메세지  
```
java.lang.IllegalStateException: Failed to load ApplicationContext
    ...
  Caused by: org.springframework.beans.factory.BeanCreationException: 
          Error creating bean with name 'jpaAuditingHandler': 
          Cannot resolve reference to bean 'jpaMappingContext' while setting constructor argument; 
          nested exception is org.springframework.beans.factory.BeanCreationException: 
          Error creating bean with name 'jpaMappingContext': 
          Invocation of init method failed; nested exception is java.lang.IllegalArgumentException: 
          JPA metamodel must not be empty!
    ...
  Caused by: org.springframework.beans.factory.BeanCreationException: 
          Error creating bean with name 'jpaMappingContext': 
          Invocation of init method failed; nested exception is java.lang.IllegalArgumentException: 
          JPA metamodel must not be empty!
    ...
  Caused by: java.lang.IllegalArgumentException: JPA metamodel must not be empty!
```
  
  
# 해결 후 코드  
```
@WebMvcTest(BoardController.class) 
@MockBean(JpaMetamodelMappingContext.class)
class BoardControllerWebMvcTest {

    private static String BOARD_MAIN_URL = "/board";
    private static String BOARD_MAIN_PAGE = "board";

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private BoardService boardService;

    @Test
    void mainPage() throws Exception{
        mockMvc.perform(get(BOARD_MAIN_URL))
                .andExpect(status().isOk())
                .andExpect(view().name(BOARD_MAIN_PAGE));
    }
```

### 해결 부분 코드  
```java
@WebMvcTest(BoardController.class) 
@MockBean(JpaMetamodelMappingContext.class)
class BoardControllerWebMvcTest {
```  

JpaMetamodel Mock을 집어넣어줘서 해결  
