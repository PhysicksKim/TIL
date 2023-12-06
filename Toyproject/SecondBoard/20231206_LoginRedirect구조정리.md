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


## 1. 전부 redirect URL 로 바꾸면 안되나요??  

이게 심플할 것 같은데 
이렇게 하면 "권한 필요 페이지 접근 시" Save Request 방식을 Redirect URL 방식으로 바꿔야 함.  
근데 Save Request Cache 동작은 ExceptionTranslationFilter 에 의해서 이뤄지고, 여기를 건드리는건 좀 복잡해질 것 같음. 

물론 할 수는 있어보임. 
AuthenticationEntryPoint 에서 GET /login 으로 Redirect 할 때 'Login 성공 Redirect URL' 을 추가할 수 있을 것 같음.  
ExceptionTranslationFilter 에서 저장한 RequestCache 를 꺼내서 Redirect URL 에다가 추가할 수 있다.  


## 2. 전부 Request Cache 로 통일하면 안되나요?  
Request Cache 로 통일하면 심플할 수 있기는 한데,  
Modal 로그인으로 접근하면 ExceptionTranslationFilter 를 거치지 않기 때문에 saveRequest() 메서드를 거칠 수  없다. 결국 수동으로 직접 Request 를 cache 하는 작업을 해야 하는데, 이 부분은 아직 직접 구현해보지는 않아서 어떤 장단점이 있을지 예측하긴 힘들다. 하지만 본질적으로 "진입점이 다양하다" 라는 문제 때문에 흐름이 더 복잡해진 거라서, 어차피 Request Cache 한다고 하더라도 마찬가지로 여러 인터페이스를 구현해야 하는 것은 변함이 없다.  
