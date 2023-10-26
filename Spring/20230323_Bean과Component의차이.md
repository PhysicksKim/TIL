# 스프링 빈 등록하는 2가지 방법  
Spring Bean 은 @Bean 을 사용하는 방식과 @Component 를 사용하는 방식 2가지가 있다.  

### 두 방식이 나뉜 이유   
| | Bean 객체 등록 방식 | 의도 |
|---|---|---| 
|@Component | 클래스에 어노테이션 추가 | 직접 만든 Class 를 Bean 등록 |
|@Bean | 메서드에 어노테이션 추가하고, <br> 등록할 Bean 객체를 Return | 외부 라이브러리 같이 <br> Class 에 어노테이션 추가할 수 없을 때 사용 |
  
## @Bean 사용법 
```
@Configuration
public class WebConfig {

  @Bean 
  public MemberService memberServce() {
    return new SimpleMemberServiceImpl();
  }
} 
```
@Bean 어노테이션을 Method 에 달아주고,   
Bean 객체는 Return 객체로 던져주면 된다      
  
<br>  
  
## @Component 사용법  

```
@Component
public class SimpleMemberServiceImpl {

  @Bean
  public MemberService memberServce() {
    return new SimpleMemberServiceImpl();
  }
} 
```
클래스에다가 @Component 달기만 하면 끝  
  
> 정확히는 @ComponentScan 이 인식할 수 있는 범위 안에 있어야 한다.
> 기본적으로 @SpringBootApplication 가 적힌 Spring Main 패키지 하위라면 자동으로 인식된다.
> @SpringBootApplication 에 @ComponentScan이 있기 때문이다.  
> 만약 @SpringBootApplication 패키지 하위가 아닌곳에 있는 @Component 클래스라면
> 따로 @ComponentScan 을 통해서 패키지를 지정해줘야 한다.  
    
  
<br><br>  
    
### 그럼 언제 클래스에 @Component를 달수 없는거지?  
라이브러리에서 제공하는 객체를 사용해야 할 때 클래스에 @Component를 달수 없다.  
  
예를 들어  
외부 PasswordEncoder 라이브러리 객체를 Bean으로 등록한다 해보자.  
@Component 를 사용해서 Bean을 등록하려면 Class 에 @Component 를 달아줘야 한다.  
그런데 외부 라이브러리라서 소스코드를 수정할 수 없다.  
따라서 @Component 대신 @Bean 을 써서, 객체 자체를 얻어온 다음 Bean 으로 등록해야 한다.  
  
```java
@Bean
public PasswordEncoder passwordEncoderBean() {
  return PasswordEncoder.getInstance(); 
}
```
  
이렇게 해주면 외부 라이브러리도 bean으로 등록할 수 있게 된다.  
  
<br><br>

# 정리  

- @Component : 클래스에다가 달아줌. Bean으로 등록. 내가 만든 클래스에 사용   
- @Bean : 메서드에다가 달아줌. Bean으로 등록. 외부 라이브러리 객체를 Bean 등록하는데 사용  
