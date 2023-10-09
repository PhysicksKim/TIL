# 둘의 차이는 목적에 있음  
- Authentication 객체 : 인증 끝나고도 회원 정보 담아서 Controller나 Service에서 쓰임
- UserDetails 객체 : 인증 확인에서만 쓰이고, 인증이 끝나면 Authentication 에 정보 집어넣어주고 객체 생명은 끝남
  
<br><br>  
  
# 흐름 
![SpringSecurity_UserDetails생명주기](https://github.com/PhysicksKim/TIL/assets/101965836/31b7af09-f106-4f47-8f93-16c66b22c79e)  
  
이름에서부터 알 수 있듯, UserDetailsService 안쪽으로 UserDetails 가 쓰인다   

### Spring Security의 인증 과정  
authenticate 과정은 아래와 같이 타고 들어간다.    
```
UsernamePasswordAuthenticationFilter
-> AuthenticationManager(구현체. ProviderManager)
-> AuthenticationProvider
-> UserDetailsService
( -> UserDetailsManager -> DB )   
```  
여기서 <code>-\> AuthenticationProvider -\> UserDetailsService</code> 이 부분을 그린게 위 차트 그림이다.   

<br>  
   
### 중요 포인트  
- Authentication 객체는  
처음에는 유저의 세부 정보를 담지 않고 들어갔다가  
인증이 끝나게 되면  
DetailsService 에 의해서 세부 정보(UserDetails)를 Authentication 객체 안에 담은채로 나온다는 점이다.   
    
- UserDetails 객체는     
UserDetailsService 아래에서 "인증이 유효한지 체크"만 하고 소멸한다.    
인증이 끝나게 되면  
UserDetails 안에 있는 정보 중에서 이후 로직에 필요할법한 내용만 Authentication 객체 안에다가 담고,  
UserDetails 객체는 UserDetailsService 안에서 소멸하고 더 이상 쓰이지 않는다.
  
<br><br>  
  
# Authentication VS UserDetails 차이  
    
  용도, 생명주기가 다르다     
Authentication 은 "유저 정보를 담는데 쓰면서, Filter 에서 생겨서 비즈니스 로직(MVC)에서 까지 쓰는 객체" 이고,      
UserDetails 는 "인증 과정에서 인증에 필요한 내용을 DB에서 가져와서, 인증 올바른지 체크에만 딱 쓰이고 죽는 객체" 이다    
  
