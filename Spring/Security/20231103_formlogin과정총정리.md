# 과정 텍스트 정리 초안  

Form Login 을 구현해주는 개발자 입장에서 전체 과정을 정리

1. UsernamePasswordAuthenticationFilter 를 구현하고 등록한다. 이 필터는 FormLogin 요청을 처리하는 필터이다. Security Config 에서 .loginProcessingUrl(String url) 을 설정해주면, 필터는 해당 URL 로 Form Login POST 요청을 받아서 처리한다.   
  
2. 앞서 말한 UsernamePasswordAuthenticationFilter 구현에 대해 설명하면, attemptAuthentication() 메서드를 오버라이드 해서 필터 로직을 처리해줘야 한다. 필터에서 처리해줘야 하는 작업은 아래와 같다.  
2-1. HttpServletRequest 에서 form data 로 넘어온 로그인 요청 정보를 추출해낸다. 예를 들어 email 과 password 를 추출한다. 
2-2. 앞서 추출한 로그인 요청 정보를 바탕으로 Token을 만든다. 예를 들어 UsernamePasswordAuthenticationToken 을 만든다. 참고로 여기서 사용한 UsernamePasswordAuthenticationToken 클래스는 Authentication 을 구현한 클래스이다. 
2-3. (선택사항) setDetails(request, token); 를 통해서 request 에서 sessionID 와 IP 를 추출해서 token 안에 details 필드에 저장한다. 여기서 setDetails 메서드는 UsernamePasswordAuthenticationFilter.setDetails() 가 먼저 호출이되고, UsernamePasswordAuthenticationFilter 에서 구현한 setDetails를 보면 그냥 단순하게 토큰의 setDetails() 메서드를 다시 호출할 뿐이다 (즉, UsernamePasswordAuthenticationToken.setDetails(...)를 할 뿐이다) 따라서 개발자가 "나는 sessionID 와 IP 말고 otp 정보나 휴대폰 인증 결과 같은 다른 정보들을 로그인 과정에서 받아서 token 안에다가 넣어두고 싶어" 라고 한다면 그냥 UsernamePasswordAuthenticationFilter 에서 setDetails 를 override 해서 수정해주면 된다.   
2-4. this.getAuthenticationManager().authenticate(token); 을 통해서 AuthenticationManager 에게 해당 token이 유효한지 인증 요청을 보낸다.   
  
3. AuthenticationManager 는 현재 서버에 등록된 AuthenticationProvider 들을 순회하면서, 전달받은 Authenticaion 객체를 해당 AuthenticationProvider 가 다룰 수 있는지 체크한다. 여기에는 AuthenticationProvider 인터페이스에서 선언된 supports() 메서드를 사용한다. 예를 들어 UsernamePasswordAuthenticationToken 을 생성해서 AuthenticationManager.authenticate(usernamePasswordAuthenticationToken) 와 같이 넘겨준 경우, authenticate() 메서드 에서는 Authentication authentication 으로 다형성을 활용해 token 을 받은 다음, 해당 authentication 객체를 처리해줄 수 있는 AuthenticationProvider 가 있는지 찾아본다. 그리고 UsernamePasswordAuthenticationToken 의 경우 DaoAuthenticationProvider 가 이를 처리해줄 수 있다고 알려준다. AuthenticationProvider 인터페이스에는 supports(Class<?> authentication) 메서드가 있는데, 이 메서드에서 전달 받은 authentication class 를 해당 구현체가 처리해줄 수 있는지 여부를 boolean으로 반환해서 AuthenticationManager 에게 알려준다. DaoAuthenticationProvider 에는 supports() 메서드에서 "return (UsernamePasswordAuthenticationToken.class.isAssignableFrom(authentication)); 이라고 코드가 작성되어 있으며, 위 코드를 보아서 DaoAuthenticationProvider 는 UsernamePasswordAuthenticationToken 을 처리하기 위해 존재함을 알 수 있다. 

4. DaoAuthenticationProvider 는 UsernamePasswordAuthenticationToken 을 전달받아서 AuthenticationProvider 에서 정의된 authenticate() 메서드를 수행해서 해당 token이 유효한 인증 요청 인지를 검사한다. 즉 인증(authentication) 과정을 수행한다. 

5. DaoAuthenticationProvider 는 이 과정에서 UserDetailsService 라는 것을 사용한다. 참고로 앞서 설명한 AuthenticationManager 는 다양한 종류의 AuthenticationProvider 중에서 전달받은 Authentication 객체를 처리할 수 있게 구현된 AuthenticationProvider를 찾고 해당 구현체로 인증 과정을 처리하도록 한다. 반면 AuthenticationProvider 에서는 각 AuthenticationProvider 에 맞춰진 "유저 정보를 전달해주는 유일한 구현체" 를 사용하도록 설계되어 있다. 즉, AuthenticationManager 는 다양한 AuthenticationProvider 를 순회하면서 어떤 구현체를 사용해야 할지 체크하도록 되어있고, AuthenticationProvider 는 "다양한 구현체를 순회해서 유저 정보를 가져오기에 알맞은 구현체를 선택" 하는 방식이 아니라 "해당 AuthenticationProvider 에게 알맞은 구현체를 유일하게 만들어 두고 이를 사용하는 방식"으로 되어있다. 
예를 들어 DaoAuthenticationProvider 의 경우에는 UserDetailsService 구현체를 유일하게 사용해서 유저 정보를 가져오도록 되어있다. OAuth2LoginAuthenticationProvider 의 경우에는 기본적으로는 DefaultOAuth2UserService 를 사용해서 유저 정보를 가져오도록 되어있다. 이처럼 AuthenticationProvider 별로 알맞은 형태로 유저 정보를 가져오도록 한다. 

---[중간 요약]---
 현재 설명은 Form Login 처리를 어떻게 할지 다루고 있다. 따라서 Form Login 에 알맞게 UsernamePasswordAuthenticationFilter 를 구현하고, UsernamePasswordAuthenticationToken 을 생성했다. 그리고 AuthenticationManager 는 UsernamePasswordAuthenticationToken 을 처리할 수 있는 DaoAuthenticationProvider 가 token 을 받아서 처리하도록 명령내린다. DaoAuthenticationProvider 는 UserDetailsService 를 사용해서 유저 정보를 가져오도록한다. 
--- --- ---

6. 개발자는 DaoAuthenticationProvider 가 UserDetailsService 를 사용할 수 있도록, 해당 백엔드 서비스에 알맞은 형태로 UserDetailsService 를 구현해주면 된다. UserDetailsService 를 구현해서 Security Config 에 등록해주게 되면, 기본으로 등록된 UserDetailsService 는 사용되지 않고 개발자가 구현해서 설정해준 UserDetailsService 가 사용된다. (다르게 말하면, 기본으로 사용되던 UserDetailsService 는 이제 DaoAuthenticationProvider 에 주입되지 않고 새로 설정된 UserDetailsService 가 DaoAuthenticationProvider 내부 필드에 주입되게 된다)  

7. UserDetailsService 인터페이스에는 loadUserByUsername(String name) 이라고 정의된 메서드 하나만 있다. 이 메서드는 전달받은 String name 을 바탕으로 DB 에서 User 정보를 가져와서 UserDetails 를 만들어서 반환하도록 되어있다. 만약 MySQL DB에 회원 정보를 담은 Member Entity 가 있고 로그인에 name 대신 email을 사용한다고 해보자. 그러면 MySQL DB 에서 Member Entity 를 email을 바탕으로 가져온 다음 UserDetails 구현체를 만들어서 반환해주면 된다.

8. UserDetails 구현체 중에는 Spring의 security.core.userdetails 에 있는 User 클래스가 있다. User 클래스는 User(username, password, authorities) 또는 각종 추가 boolean 값들을 포함한 생성자를 제공한다. 간단하게 개발자 해야 할 행동을 설명하면, 개발자는 DB 에서 가져온 유저 정보를 바탕으로 User 객체를 생성해주면 된다. User 객체 생성에는 간단한 방식으로 로그인 에사용 하는 유저 식별자(username) 과 비밀번호, 그리고 각종 권한 정보를 담은 authorities collection을 통해서 User 객체를 생성해주면 된다.

9. UserDetailsService 로 부터 UserDetails 를 구현한 객체를 전달 받은 DaoAuthenticationProvider 는 이제 인증 로직을 진행한다. Request 로 부터 넘어온 Password 와 Request 의 username(유저 로그인용 식별자) 로 부터 DB에서 가져온 UserDetails 를 비교해서 password 가 일치 하는 지를 체크하는 로직이 이제 여기서 실행된다. 

[코드 수준의 설명]
좀 더 자세하게 코드를 보려면 DaoAuthenticationProvider 와 더불어, DaoAuthenticationProvider 의 부모인 AbstractUserDetailsAuthenticationProvider 를 번갈아 가면서 코드를 봐야 한다. 
일단 이름이 기니까 DaoAuthProvider 와 AbstractAuthProvider 라고 줄여서 설명하겠다. 
큰 틀이 되는 authenticate() 메서드는 AbstractAuthProvider 에 작성되어 있다. 여기서 로직의 순서는 
1) retrieveUser() 메서드를 통해서 UserDetails 를 가져옴 
2) 가져온 UserDetails 를 Request로부터 얻은 Authentication 객체와 비교해서 passwordEncoder.matches() 를 수행해 비밀번호가 일치하는지 체크함 
3) 앞선 2)에서 비밀번호 체크 및 기타 조건들(ex. 해당 계정이 만료된 계정이 아닌지 등)을 통과했다면, Authentication 객체에 userDetails 에 담긴 값들을 집어 넣어주고 return 한다. 

1)에서 retrieveUser() 를 호출해서 User 를 가져오는 코드는 AbstractAuthProvider 에서 authenticate() 메서드에 있다. retrieveUser() 메서드의 정의는 DaoAuthProvider 에 있다.   
2)의 passwordEncoder.matches() 가 이뤄지는 코드의 첫 호출은 AbstractAuthProvider 에서 authenticate() 메서드에서 additionalAuthenticationChecks(...) 에서부터 시작된다. additionalAuthenticationChecks() 메서드는 DaoAuthProvider 에서 구현되어 있다. 
DaoAuthProvider 에서 additionalAuthenticationChecks() 메서드 구현을 보면 this.passwordEncoder.matches(...) 를 통해서 비밀번호가 일치하는지 체크하고 있다.  
3)의 작업은 createSuccessAuthentication() 메서드에 의해서 이뤄진다. 이 메서드는 AbstractAuthProvider 와 DaoAuthProvider 둘 다 구현되어 있다. 이 경우 DaoAuthProvider 가 해당 메서드를 override 하고 있는 구조다. 여기서 착각할 수 있는 부분은 DaoAuthProvider 에 있는 createSuccessAuthentication() 코드만 수행한다는 생각이다. 물론 Override 하면 자식 구현체인 DaoAuthProvider 에 있는 코드를 우선 수행하게 되지만, DaoAuthProvider 에서 createSuccessAuthentication() 코드의 끝에 super.createSuccessAuthentication() 를 수행하고 있으므로 AbstractAuthProvider 의 createSuccessAuthentication() 도 실행된다. 
DaoAuthProvider 에서 Override 한 부분은 그냥 PasswordEncoder 에 따라서 upgradeEnding 을 체크해서 패스워드 인코딩 방식을 새롭게 업그레이드 해야 하는지 만 체크하고 있다.
AbstractAuthProvider 에서 createSuccessAuthentication() 코드는 authenticated = true 라고 되어있는 Authentication 객체 (=UsernamePasswordAuthenticationToken 객체)를 생성해주고, details 를 Authentication 객체에 추가해준 다음 새로 생성된 Return 용 Authentication 객체를 반환한다. 여기서 createSuccessAuthentication() 로 생성된 Authentication 객체는 이제 최종적으로 AuthenticationProvider 를 벗어나서 AuthenticationManager 에게 전달된다.

[흥미로운 부분] 참고로 AbstractUserDetailsAuthenticationProvider 에 authenticate() 메서드가 구현되어 있는데, 여기서 userCahe 가 사용됨을 볼 수 있다. 실제 코드에 따르면 UserDetailsService 를 통해서 loadUserByUsername 을 먼저 하지 않고, 앞서서 해당 user 가 있는지 cache 에서 찾고, 없으면 UserDetailsService 를 통해서 retrieveUser 를 하고 있다. 만약 차후에 UserCache 를 사용하고자 한다면 userCache 를 구현해서 넣어주면 될 것 같다. 

9. AuthenticationManager(구현체 ProviderManager)는 이제 인증 성공한 Authentication 객체를 받은 다음 credential 을 제거하는 작업을 한다 

10. 이후 마지막으로 이 과정의 시작인 UsernamePasswordAuthenticationFilter 로 돌아와서 attemptAuthentication() 메서드를 끝마치고 Authentcation 객체를 리턴한다. 

11. attemptAuthentication() 메서드 호출은 UsernamePasswordAuthenticationFilter 의 부모인 AbstractAuthenticationProcessingFilter 에 의해서 이뤄진다. 여기서는 attemptAuthentication() 결과로 받은 인증 성공한 Authentication 객체를 처리하기 위해서 successfulAuthentication() 메서드를 실행해서 SecurityContext 에 성공한 Authentication 객체를 집어넣는다. SecurityContext에 저장된 Authentication 객체는 필터 처리가 끝난 후 Controller, Service 계층에서 쓰일 수 있다. 그리고 이 과정에서는 SecurityContext, SecurityContextHolder, SecuritContextRepository 등이 쓰이는데 이는 또 복잡한 설명이 필요하므로 생략한다.  
