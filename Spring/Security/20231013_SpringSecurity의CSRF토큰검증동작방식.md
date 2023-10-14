# Spring Security의 CSRF 토큰 관리 방식  
  
Spring Security 에서는 아래와 같이 CSRF 토큰 및 인증을 관리한다.    
  
1. Client 가 직후 CSRF Token이 필요한 페이지로 접근한다. (ex. 회원 정보 수정 페이지 get 요청)        
2. 서버는 해당 페이지가 차후 CSRF Token이 필요함을 인지하고, CSRF Token을 생성할때는 JSESSIONID 와 함께 CSRF Token을 생성해서 Response에 담고, 서버 메모리에 {CSRF Token - JSESSIONID}를 저장해둔다.  
3. Client 는 Response 에 담긴 CSRF Token을 저장해두고, 다음 Request를 보낼 때 HTTP Header에 CSRF Token을 담아서 보낸다.   
4. 서버는 Request 에서 CSRF Token과 JSESSIONID를 추출한다.   
5. 앞서 (2) 과정에서 서버 메모리에 저장해둔 {CSRF Token - JSESSIONID} 와 Request 에서 추출한 {CSRF Token - JSESSIONID} 가 일치하는지 확인한다. 일치하면 통과하고 로직을 수행한다.    
  
<br><br>  
  
# csrf token 생성 시 CsrfFilter 흐름 

![SpringSecurity_CsrfFilter에서csrfToken생성흐름](https://github.com/PhysicksKim/TIL/assets/101965836/31da7067-3f25-4e28-a845-68a0b7dd4c4a)
