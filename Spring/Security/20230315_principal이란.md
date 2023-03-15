> 나는 principal은 OAuth2 에서 처음 만났지만   
> Spring Security에서 제시하는 개념이다  
  
[Spring Security 로그인 구조 관련 한글 포스팅](https://codevang.tistory.com/266)  
  
# Principal  
[참고](https://stackoverflow.com/questions/37499307/whats-the-principal-in-spring-security)  
  
> A Principal represents a user's identity.  
>   
> It can be a String object having username on a simple level or a complex UserDetails object.  
>    
> - 내 해석  
> Principal은 유저의 고유 정보를 나타내는 객체이다  
> username 을 담은 간단한 String 일수도 있고, 복잡하게 유저의 정보들을 담은 UserDetails 객체일 수도 있다.  
  
### Principal 과 User 는 뭔 차이?    
  
> After successful authentication,   
> we have a populated subject with many associated identities, 
> such as roles, social security number(SSN), etc.   
> In other words, these identifiers are principals, and the subject represents them.  
>     
> - 해석  
> 인증에 성공한 후에,  
> role이나 주민번호같은 고유값 같은 것들로 채워진 객체를 얻는다.  
> 달리 말하면, 이러한 고유값들을 principal 이라고 하고, 객체가 principal들을 나타낸다.  
  
