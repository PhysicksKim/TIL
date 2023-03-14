# Spring Security 사용시에 쓰는 @EnableWebSecurity 
  
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig { 
  ...
}
```
Security 를 처음 보면 위와 같이 @EnableWebSecurity 어노테이션을 볼 수 있다.  
  
  
### docs 의 설명  
> Add this annotation to an @Configuration class to have the Spring Security configuration   
> defined in any WebSecurityConfigurer or more likely by exposing a SecurityFilterChain bean:  
>     
> - 발번역     
> 이 어노테이션을 @Configuration 클래스에 추가하면  
> WebSecurityConfigurer 나 SecurityFilterChain bean으로 정의되는 Spring Security 설정을 할 수 있다.  
  
<br>

### @Configuration 을 같이 쓰는 이유  

최초에 @EnableWebSecurity 가 추가된 시점에는  
@Configuration 이 같이 들어가있지 않아서 @Configuration 을 같이 추가해줬어야 했다.  
  
하지만 이후 버전에 @EnableWebSecurity 어노테이션 안에 @Configuration 이 추가되어서  
꼭 @Configuration 을 추가해줄 필요는 없다.  
다만 하위호환성이 혹시 필요할 지 모르니 추가해줘도 나쁠건 없다.  
  
<br>  
  
### 추가 설명  
[참고](https://stackoverflow.com/questions/44671457/what-is-the-use-of-enablewebsecurity-in-spring)  
  
핵심은 "스프링이 웹 보안을 활성화하고, 관련 configuration 들을 인식하도록 하기" 이다.    
   
@Bean 으로 시큐리티 필터를 스프링 필터 체인에 등록하도록 해준다.  

<br>  

### 추가 + 추가 설명 - @EnableWebSecurity 어노테이션 없어도 동작한다는 질문   
[참고2](https://stackoverflow.com/questions/71186172/reason-for-enablewebsecurity-in-the-configuration-class)  
  
스프링 부트를 쓰지 않는 순수 스프링 프로젝트라면 @EnableWebSecurity 를 써야지 스프링 시큐리티가 활성화 된다.  
  
그치만  
스프링 부트 2.0 이상을 쓴다면   
스프링 부트가 알아서 WebSecurityEnablerConiguration 에 의해 @EnableWebSecurity 가 자동으로 추가된다.  
  
### 어? 난 제대로 동작 안하는데  
> 근데 난 WebSecurityConfigurerAdapter 안써서 그런가, @EnableWebSecurity 없으면 제대로 동작 안하는데?  
   
어 이거  
@EnableWebSecurity 어노테이션 제거하니까  
저번에 봤던 
모든 페이지에 OAuth 로그인 화면 뜨는 문제가 생기네?    
  
<br>  

# @EnableWebSecurity 를 제거해보니, 그 클래스에 넣은 필터 체인이 인식이 안되네!!  
  
[로그인 화면 계속 뜨는 문제](https://github.com/PhysicksKim/TIL/blob/main/Toyproject/SecondBoard/20230309_SpringSecurity%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%99%94%EB%A9%B4.md)  
  
아! 이전에 있던 문제가 이거 때문이었나보다  
  
이전에  
index 페이지는 permitAll() 해뒀는데도 OAuth 로그인 페이지가 계속 뜨던 문제가 있었는데  
그 때 아마 @EnableWebSecurity 어노테이션을 안달았어서 문제가 발생했나보다.  
  
### @Bean 달아준 filterChain() 이 인식안됨  
```java
// @EnableWebSecurity
public class SecurityConfig {

  @Bean
  public SecurityFilterChain filterChain(HttpSecurity http) throws Exception
```  
요렇게 필터체인 넣어줘도  
@EnableWebSecurity 를 안달아줘버리면  
스프링에 인식을 못하는구나    
  
<br><br><br>  

---

<br><br><br>  
  
# 결론  
  
1. 필터 체인으로 HttpSecurity 를 설정해줘야 한다  
2. 그래서 @EnableWebSecurity 어노테이션을 클래스에 달아줘서  
3. 스프링이 필터 체인 메서드를 인식하도록 한다  
    


