# 로그인 성공 후 Redirect 
로그인 성공 후 Redirect 하려면 이전에 접근한 페이지를 기록해서 Client 또는 Server Session 에 기록해둬야 한다.  
  
1. 쿼리 파라미터 URL 남기기  
2. Form Hidden 값으로 URL 남기기  
3. Server Session 에 남기기  
  
3가지 방법이 있다.  

# Redirect 구현이 복잡해졌다  
결론부터 말하면 3가지 방법 다 써야 하게 됐다.  
Spring Security 의 login 구조를 활용하는 게  
차후 권한 필요 페이지 관리에 좋기 때문에 Security 아키텍쳐에 따라야 한다.  

근데 Security 아키텍처에는 Login 성공 후 Redirect 를 지원하기는 하지만  
내가 구현해둔 Modal Login / Login Page 둘을 병행하는 방식에서는   
기존에 Redirect 방식(Save Request 방식)이 제대로 동작하지 않는다.  
그래서 일단 앞서 말한 3가지 방식을 다 사용하도록 구현했다.  


![image](https://github.com/PhysicksKim/TIL/assets/101965836/3ecf021e-def3-4918-a8c7-d35b9f7dc0fb)  

# 왜 이렇게 복잡해  

### 로그인 성공 Redirect 방법
A. query parameter RedirectURL
B. Form Hidden 값  
C. Save Request Cache  

<br>

## 1. 전부 redirect URL 로 바꾸면 안되나요??  

이게 심플할 것 같은데 
이렇게 하면 "권한 필요 페이지 접근 시" Save Request 방식을 Redirect URL 방식으로 바꿔야 함.  
근데 Save Request Cache 동작은 ExceptionTranslationFilter 에 의해서 이뤄지고, 여기를 건드리는건 좀 복잡해질 것 같음. 

물론 할 수는 있어보임. 
AuthenticationEntryPoint 에서 GET /login 으로 Redirect 할 때 'Login 성공 Redirect URL' 을 추가할 수 있을 것 같음.  
ExceptionTranslationFilter 에서 저장한 RequestCache 를 꺼내서 Redirect URL 에다가 추가할 수 있다.  

<br>
  
## 2. 전부 Request Cache 로 통일하면 안되나요?  
Request Cache 로 통일하면 심플할 수 있기는 하지만 구현이 어렵다.  
  
그리고 결정적으로 Modal Login Redirect 처리는 RequestCache 기반으로 곧바로 할 수 없다.  
  
### Modal Login 문제점  
Request Cache 기반 Redirect 는 Request URL 로 다시 보내는 방식으로 동작한다.  
반면 Modal Login Success Redirect 는 Referer URL 로 다시 보내는 방식으로 동작한다.  
  
따라서 Modal Login 의 request 를 곧바로 cache 에 넣는다고 동작하는 게 아니다.     
Modal Login Redirect 에서 Request Cache 방식을 사용하려면  
referer URL 을 빼낸 다음 Servlet Request 를 억지로 만들어서 request URL 에 넣어줘야 한다.    
  
사실 모든 문제의 원흉은 Modal Login 이긴 하다.  
하지만 사용자 경험을 위해서 Modal Login 을 남겨두고, 구조적인 문제를 감수하는 게 좋지 않을까 싶어서 일단 Modal Login을 남겨두자.  
  
