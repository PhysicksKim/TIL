# 스프링 빈 등록하는 2가지 방법  
1. @Bean 을 사용한다  
2. @Component 를 사용한다  
  
둘 다 스프링 빈으로 등록할 때 사용하는 어노테이션이다.  
그런데 왜 둘로 나뉘어 있을까?  
  
그 이유는   
@Bean 은 메서드에다가 달아주는 어노테이션이고  
@Component 는 클래스에다가 달아주는 어노테이션이다.  
  
근데   
메서드에 달아주는 어노테이션은 뭐지?  
  
<br><br>  
  
# @Bean vs @Component
  
1. 메서드에 @Bean 달아준다 -> return 타입으로 bean 으로 등록한다  
2. 클래스에 @Component 달아준다 -> 해당 클래스 타입 자체를 bean 으로 등록한다    
   
## 클래스를 @Component 로 Bean 등록하는 건 이해가 간다  
Bean 자체가 @Autowired 필요한 곳에다가 알맞은 구현체를 알아서 넣어주는 DI를 해주기 위함이다.  
그러니까 클래스가 곧 Type 이니까   
클래스에다가 @Component를 달아서 Bean으로 등록하는건 이해가 쉽다.  
  
<br>  

## 메서드에 @Bean 으로 Bean 등록하는건? 
일단.  
클래스에 @Comopnent를 달아서 Bean 등록할 수 없을 때 쓰는거다.  
    
### 그럼 언제 클래스에 @Component를 달수 없는거지?  
라이브러리에서 제공하는 객체를 사용해야 할 때 클래스에 @Component를 달수 없다.  
  
예를 들어  
PasswordEncoder 라고 라이브러리가 있다고 해보자.  
근데 Class PasswordEncoder 로 가서 @Component 를 달수 없다.  
왜냐하면 당연하게도 외부 라이브러리니까 소스코드에다가 @Component를 달아줄 수 없으니까!  
  
그래서 따로 메서드에다가 리턴 타입을 PasswordEncoder 라고 해주고 @Bean 애노테이션을 달아주는거다.  
  
```java
@Bean
public PasswordEncoder passwordEncoderBean() {
  return PasswordEncoder.getInstance(); 
}
```
  
이렇게 해주면 외부 라이브러리도 bean으로 등록할 수 있게 된다.  
  
<br><br>

# 정리  

- @Component : 클래스에다가 달아줌. Bean으로 등록. 내가 만든 클래스(타입)에 사용   
- @Bean : 메서드에다가 달아줌. Bean으로 등록. 외부 라이브러리 객체를 Bean 등록하는데 사용  
