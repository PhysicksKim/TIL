# 문제 발생  
URL에 상관없이 무조건 localhost:8080 로 접근하면 Oauth2 로그인 화면이 떴다  
  
왜일까 싶어서  
그냥 모든 Oauth2 관련 설정을 비활성화 했더니  
이번에는 Oauth2 가 아니라  
Spring Security의 Login 페이지가 떴다  
  
<br>
  
## 가설 - SpringSecurity 가 모든 페이지로 접근할 때 인증을 요구해서 로그인 페이지로 연결시킨다  
  
Spring Security 에 별다른 설정을 안하면  
모든 페이지에 인증된 사용자만 접근할 수 있도록 하는 것 같다.     
  
그리고 별다른 인증 시스템이 없으면  
자체 Spring Security의 인증 시스템을 사용하도록 하는 것 같다.  
  
따라서  
1. 어느 페이지가 인증이 필요한지 설정      
2. 인증 시스템이 Oauth를 사용하도록 설정    

이 2가지를 설정 문제인 것 같다.  

<br><br>

---

<br><br>

# Spring Security Filter에 의해 인증로직을 거치게 됨  

[출처 Spring Security Tutorial - [NEW] [2023] - Explain form login(14:46)](https://youtu.be/b9O9NI-RJ3o?t=874)  
  
> 어플리케이션이 처음 시작될때 스프링 시큐리티는 시큐리티 필터 체인 타입의 빈을 먼저 찾게 된다  
> SecurityFilterChain 이라는 interface를 들어가서 Navigate to the spring bean declaration 을 통해 들어가보면  
> SpringBootWebSecurityConfiguration 클래스   
> -> SecurityFilterChainConfiguration 내부 스테틱 클래스   
> -> defaultSecurityFilterChain() 메서드 를 보면    
> HttpSecurity http 를 받아서   
> ```
> SecurityFilterChain defaultSecurityFilterChain(HttpSecurity http) throws Exception {
>   http.authorizeRequests().anyRequest().authenticated();  
>   http.formLogin();
>   http.httpBasic();
>   return http.build();
> }
> ```
>  
> 이렇게 http anyRequest()가 들어오면 무조건 authenticated() 로 검증을 거치고 .formLogin() 을 실행함을 알 수 있다.    
> 다르게 말하면, http.authorizeRequests().anyRequest().authenticated(); 이 부분 때문에 곧바로 endpoint에 접근할 수 없게 돼서   
> Spring은 모든 request에 대해서 저 코드를 실행하므로, 항상 authenticated() 를 실행해 검증하게 된다.  
>   
> 로그인 페이지가 뜨는 이유는 http.formLogin() 를 실행하기 때문이다.    

<br><br>  

---

# 2가지 문제 겹침 - 1)타임리프문법 2)SecurityConfig

## 1) 타임리프 문법 - TemplateParseError 
이건 그냥 사소한 타임리프 문법 문제였다.  
  
중간에 실수로   
```
<div th:> 
```
라는 태그가 끼여 들어가 있어서 에러가 났었다.  
  
별로 안중요.  
  
<br>  
  
## 2) SecurityConfig

[코드 출처](https://velog.io/@ncookie/WebSecurityConfigurerAdapter-Deprecated-%EB%AC%B8%EC%A0%9C-%ED%95%B4%EA%B2%B0)  
  
처음에는 아래와 같이 HttpSecurity 를 설정했었다.  
  
```java
@Bean
public SecurityFilterChain filterChain(
                  HttpSecurity http) throws Exception {
  http
      // h2-console 화면을 사용하기 위해 해당 옵션들을 disable
      .csrf().disable()
      .headers().frameOptions().disable()
      .and()
        // URL 별 권한 관리를 설정하는 옵션의 시작점
        .authorizeRequests()
        .antMatchers("/", "/css/**", "/images/**",
              "/js/**", "/h2-console/**").permitAll()
        .antMatchers("/api/v1/**").hasRole(Role.USER.name())
        .anyRequest().authenticated()
      .and()
        .logout()
          .logoutSuccessUrl("/")
      .and()
        .oauth2Login()
          .userInfoEndpoint()
              .userService(customOAuth2UserService);

  return http.build();
}
```
[HttpSecurity - Spring Docs](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/config/annotation/web/builders/HttpSecurity.html)  
[참고1 - [Spring Security] 권한 설정 및 표현식](https://fenderist.tistory.com/411)    
  


```
.and()
  // URL 별 권한 관리를 설정하는 옵션의 시작점
  .authorizeRequests()
  .antMatchers("/", "/css/**", "/images/**",
        "/js/**", "/h2-console/**").permitAll()
  .antMatchers("/api/v1/**").hasRole(Role.USER.name())
  .anyRequest().authenticated()
.and() 
```  
.authorizeRequests() 를 시작으로 하는     
권한 있는지를 검사하는 코드다.  
  
뭐가 문제였을까?  
  
### 위 부분을 말로 다시 풀어보자  

```
.authorizeRequests() // 권한 검사 설정 코드 시작  

.antMatchers(...) // 여기 적힌 URL 에 대해서는  
.permitAll() // 전부 그냥 접근을 허가한다  

.antMatchers(...) // 여기 적힌 URL 에 대해서는  
.hasRole(Role.User.name()) // Role.User.name() = "ROLE_USER" 에 해당하는 사용자만 접근허용  

.anyRequest() // any 요청들에 대해서는 
.authenticated() // 권한이 부여된 경우에만 허가한다  
```
  
흠...  
맞는 거 같은데?  
  
<br><br> 

---

<br><br>  

# 그냥 에러 나던 Commit 부터 다시 해봄  

<br> 

## 1. application-oauth.properties 불러오기 에러 - spring.profiles.include  
  
처음 config 패키지 까지만 작성했던 코드 가져와서 실행해보니  
```
Description:

Method springSecurityFilterChain in org.springframework.security.config.annotation.web.configuration.WebSecurityConfiguration required a bean of type 'org.springframework.security.oauth2.client.registration.ClientRegistrationRepository' that could not be found.


Action:

Consider defining a bean of type 'org.springframework.security.oauth2.client.registration.ClientRegistrationRepository' in your configuration.
```

이렇게 에러가 났다.  

찾아보니 oauth 관련 설정 값이 제대로 안들어가서  
위에서 말하는 bean 이 제대로 생성되지 않은 이유로 발생한 문제라고 한다. [출처](https://stackoverflow.com/questions/62446695/consider-defining-a-bean-of-type-org-springframework-security-oauth2-client-reg)  
  
그래서 위 출처의 내용에 따라   
아래와 같이 spring.profiles.include 를 추가해줬다   
  
```
spring:
  config:
    import:
      - application-oauth.yml
      - optional:application-db.yml
  profiles:
    active:
      - dev
      - optional:oauth
    include: oauth  
```

<br><br>  
  
## 2. 어? 왜 되는건데  

> 아! 안되는 상태일떄 임시로 commit 그냥 해놓을껄  
> 왜 되는지 모르겠네.  
  
일단 계속 가보자  

### 잘 되는 버전의 파일들 차례대로 집어넣기  
index.html 파일을 main/resources/templates/index.html 로 넣어주고 돌려보니  
oauth 로그인 창까지 정상적으로 들어가진다.  
  
그 다음 domain/index/IndexController.java 파일을 넣어주고 돌려보니  
로그인도 잘 되는 모습을 볼 수 있다.  
  
### 다 잘 되는데?  

어?  

<br><br><br>  

---

<br><br><br>  

# 아무튼 해결됨  
  
음. 왜 그랬을까 그냥 추측해보기로는
  
1. oauth 설정 파일 문제    
2. config 에서 SecurityConfig 코드 문제 - WebSecurityConfigurerAdapter 가 Deprecated 되어서 수정했다가 문제 발생  
3. Thymeleaf 문법 parse error  
  
이 정도 문제가 겹친 것 같다.  
  
아... 문제있을 때 commit 해둘 껄  
  
<br>  
  
## 얻은 지식 - Spring Security  
그냥 oauth 예제코드 날로 먹으려 했는데  
마침 예외가 터져서 더 자세히 공부하게 됐다  
  
덕분에 Amigocode - spring security tutorial 쭉 배웠고,    
HttpSecurity 라던가 Spring Security 에 대해 처음 발을 들이는 계기가 됐다    
    
