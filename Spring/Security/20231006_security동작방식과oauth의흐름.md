# 출처  
[연 우리 - \[Spring Security\] 동작방법 및 Form, OAuth 로그인하기 (Feat.Thymeleaf 타임리프)](https://lotuus.tistory.com/78)  
위 블로그 글이 잘 정리되어 있고, 위 블로그 포스팅을 바탕으로 다시 정리해서 글 작성  
  
<br><br><br>  
  
# Security 의 흐름  

### 흐름 차트를 보기 전에    
![image](https://github.com/PhysicksKim/TIL/assets/101965836/f9b614aa-b36a-4def-8739-6a8b078fdc21)  
  
흐름 차트 부터 보면 어렵다.  
  
위 차트에 대해 알아보기 전에 간단한 원리부터 알고 들어가자.  
  
---
  
# Authentication 과정을 간단히 말하면  
인증 과정을 간단히 말하면  
  
1. 요청으로 적어보낸 id password 를 담은 객체를 만든다. 이 객체가 인증 과정에서 값을 담아서 돌아다님  
2. 해당 id password 가 맞는지 확인    
3. 인증 실패하면 Exception 던지고, 인증 성공하면 ID PASSWORD 담긴 객체를 security context에 넣음
  
이렇게 된다.  
실상 큰 흐름 자체는 별로 어려운게 없는데, 아무래도 프레임워크 구조상 이리저리 추상화 되어 있어서 어려워 보이는거다.  
  
<br><br>  
  
### 위 과정을 Spring Security 에서는..  
이제 위 1,2,3 과정을 Spring Security 에서 쓰는 말로 바꾸면 이렇다.  

1. id password 담긴 객체 - "Authentication" 객체
2. AuthenticationProvider가 "Authentication" 객체에 담긴 ID Password가 알맞은지 확인한다. 여기서 ID에 따라 어떻게 유저를 가져오는지는 개발자가 Service로 구현한다      
3. Authentication 객체는 Controller에서 Argument로 자동으로 받을 수 있다. 또는 Security Context에 직접 접근해서 가져올 수도 있다.
  
<br><br>  

# Provider 와 Service 
AuthenticationProvider 와 ~UserService 가 각각 뭔지 헷갈렸는데 딱 정리한다.  

- ~UserService : 회원 식별 정보(ex.ID)를 바탕으로 인증 여부를 검증할 값(ex. password)을 가져와주는 역할  
- AuthenticationProvider : Request 로 넘어온 값 ID/PASSWORD (Authentication 객체)와 Service로 가져온 올바른 ID/PASSWORD 를 비교하는 역할  
  
Service 가 유저 정보를 제공해주고, AuthenticationProvider 가 Request로 날아온 정보와 올바른 유저 정보를 비교해서 인증 성공 여부를 결정해준다.  
  


