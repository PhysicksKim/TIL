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
  
