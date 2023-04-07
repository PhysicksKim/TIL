> 나는 principal은 OAuth2 에서 처음 만났지만   
> Spring Security에서 제시하는 개념이다  
  
[Spring Security 로그인 구조 관련 한글 포스팅](https://codevang.tistory.com/266)  
[Principal (computer security) - wiki](https://en.wikipedia.org/wiki/Principal_(computer_security))  

# 요약  
1. Principal은 컴퓨터 보안 관련된 용어로, 시스템이 식별하고 인증한 사용자, 그룹 또는 서비스를 의미한다.    
2. Principal은 인증된 사용자 라는 의미다  
3. PrincipalDetails는 인증된 사용자의 세부 정보들 이라는 의미다  
  
<br><br>  
  
# "Principal" 이란

> ### 영문 위키 Principal (computer security)  
> A principal in computer security 
> is an entity that can be authenticated by a computer system or network. 
> It is referred to 
> as a security principal 
> in Java and Microsoft literature.  
  
컴퓨터 보안에서 pricipal은 인증된 객체를 말한다  
Java나 MS에서는 Security principal 이라고 칭한다.  
  
<br><br>  
  
# User 객체와 Spring Security의 PrincipalDetails가 왜 따로있지?    
  
- 처음에 든 의문  
둘 다 그냥    
Request에서 사용자의 인증 정보를 담은 객체 아닌가?  
근데 무슨 차이가 있어서 따로 PrincipalDetails 라는 객체가 있는거지?  
  
<br>  
  
### 분리 이유 : 개발자가 쓰는 User 객체 / Spring Security가 쓰는 \_\_\_Details 객체를 분리  
  
객체를 분리하기 위함이다  
관심사의 분리 원칙에 따라서   
Security에게 필요한 값이 담긴 \_\_\_Details 객체와  
Controller 단계에서 필요한 User 객체의 관심사를 분리하기 위함이다  
  
<br>  
  
### 추가 설명  
- 만약 User 구현체가 UserDetails 인터페이스를 구현한다면?  
User는 controller 에서 사용할 용도의 객체이면서 동시에   
Spring Security에서 사용하게 된다  
그런데 만약 Security에서 어떤 작업을 하려고 User 구현체에 변경이 생기면  
그게 controller에서 사용하는 로직에도 영향을 미치게 된다.  
반대로 controller에서 어떤 변경에 의해서 User 객체가 바뀌면  
그게 또 Spring Security 로직에 문제가 생길 수 있다.  
따라서 관심사의 분리 원칙에 의거해서  
User 클래스 -> Controller 에서 사용하는 용도  
  
<br>  

### 예시) Security 로직이 바뀌는 경우    
예를 들어 id와 password로 인증을 하다가  
Email 과 password로 인증하도록 바뀌었다고 해보자  
그러면 Securirty에서만 인증 로직이 바뀌면 되는 데  
만약 Controller 에서 사용하는 객체를 그대로 Security에 사용하게 된다면 문제가 생긴다  
그래서 DTO, DAO 같은 것들을 다 분리해주는 것이다.  
  

