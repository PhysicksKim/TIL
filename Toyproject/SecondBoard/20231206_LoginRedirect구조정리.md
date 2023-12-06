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
