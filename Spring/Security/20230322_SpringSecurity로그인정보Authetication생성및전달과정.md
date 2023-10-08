# SpringSecurity 에서 로그인시 어떻게 사용자 인증이 이뤄지지?
  
### Chat GPT의 정리  

1. 사용자가 입력한 인증 정보로 UsernamePasswordAuthenticationFilter에서 Authentication 객체가 생성되고, Authentication Manager에 전달됩니다.   
2. Authentication Manager는 Authentication 객체에서 사용자 이름을 추출하고, UserDetailsService에서 이를 이용하여 UserDetails 객체를 조회합니다.   
3. 조회된 UserDetails 객체와 Authentication 객체의 인증 정보를 비교하여 인증에 성공하면, Authentication 객체를 SecurityContextHolder에 저장하여 인증을 완료합니다.   
  
위 과정을 통해 Spring Security는 사용자 인증을 처리합니다.   
각 단계에서는 많은 객체와 인터페이스가 사용되지만,   
이를 통합적으로 다루는 Spring Security 프레임워크가   
안정적인 인증 처리를 제공합니다.  
  
### 로그인 처리 과정 손그림  
    
![KakaoTalk_20230322_234841978](https://user-images.githubusercontent.com/101965836/226943270-1af66fc3-f52d-43f9-9e9a-21553cbca0ea.jpg)  
  
  
# 설명  

<pre>
좌측 위 "클라이언트" 부분 부터 처리 로직이 시작됩니다.  
  
1. 클라이언트 요청: 클라이언트로부터 로그인 정보가 포함된 HTTP 요청이 발생합니다.
2. UsernamePasswordAuthenticationFilter: 이 필터는 HTTP 요청에서 username과 password를 추출하여 Authentication 객체를 생성합니다.
3. Authentication Manager: UsernamePasswordAuthenticationFilter에서 생성된 Authentication 객체는 AuthenticationManager에게 전달됩니다.
4. UserDetailsService: AuthenticationManager는 UserDetailsService를 통해 DB나 다른 저장소에서 해당 username에 매칭되는 회원 정보를 가져옵니다.
5. 인증 체크: UserDetailsService에서 가져온 회원 정보와 Authentication 객체에 저장된 정보를 비교하여 인증이 유효한지 확인합니다.
6. Authentication 객체 저장: 인증에 성공하면, 해당 Authentication 객체는 SecurityContext에 저장됩니다.
7. Controller와 Service: SecurityContext에 저장된 Authentication 객체는 Controller의 파라미터로 전달될 수 있고, SecurityContextHolder를 통해 서비스 계층에서도 접근 가능합니다.
</pre>

<br><br>  

# 기본 Spring Security 로그인 Form 요청시 처리 과정  

![image](https://github.com/PhysicksKim/TIL/assets/101965836/4bcb7bf6-b5cd-4264-aa57-595ce67a6fb4)
  

![image](https://github.com/PhysicksKim/TIL/assets/101965836/bb17c3ea-2ce8-4e50-88d4-ef14957a4d69)  
  
