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
