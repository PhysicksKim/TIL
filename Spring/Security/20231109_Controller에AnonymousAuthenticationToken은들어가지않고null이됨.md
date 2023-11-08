# 비로그인 경우 authentication 객체는 null 이 됨  

```java
@Controller
public class IndexController {

  @GetMapping("/")
  public String index(Authentication authentication) {
      log.info("auth username : {}", authentication != null ? authentication : "NULL");
      Authentication authentication2 = SecurityContextHolder.getContext().getAuthentication();
      log.info("직접 받은 인증객체 : {}", authentication2);
      return "index";
  }
}
```

```
auth username : NULL
직접 받은 인증객체 : AnonymousAuthenticationToken
```

<br><br>  

# AnonymousAuthenticationToken 이 들어가지 않는 이유   
[참고1 - stackoverflow 질문글](https://stackoverflow.com/questions/60094034/is-there-a-way-to-get-a-non-null-principal-authentication-using-spring-security)    
[참고2 - 위 질문 글에서 타고 들어간 git issue](https://github.com/spring-projects/spring-security/issues/4011)   
  
```java
public class SecurityContextHolderAwareRequestWrapper extends HttpServletRequestWrapper {

  // 생략

  private Authentication getAuthentication() {
		Authentication auth = SecurityContextHolder.getContext().getAuthentication();
		return (!this.trustResolver.isAnonymous(auth)) ? auth : null;
	}
}
```

위 소스 코드를 보면 Anonymous 인 경우 null 을 반환하도록 되어있다.  
따라서 익명인 경우 AnonymousAuthenticationToken 이 SecurityContext 에는 들어가있지만, ArgumentResolve 과정에서는 들어가지 않는다.  

<br>  

# 이유 추측  

null 을 넣어주는 명확한 이유를 찾지 못했지만  
추측해보면 null 로 인증되지 않음을 알리는 게 더 좋다고 생각한 것 같다.  
  
null 은 항상 문제를 일으킨다.  
하지만 어쩄거나 객체로 들어오게 된다면, 개발자가 비회원인 경우를 고려하지 않은 경우에도 로직이 그대로 동작해버릴 수 있다.  
따라서 비회원인 경우에는 null 검사를 통해 로직을 분기하도록 하는게 더 좋다고 판단한 것이다.  
  
<br>
  
## 예시 1. 비회원 null 대신 AnonymousAuthenticationToken 이 들어온다면  

```java
public String onlyForMember(Authentication auth) {
  memberService.do(auth);
}
```
  
만약 null이 아니라 구현된 token 객체가 들어온다면 
위 memberService.do(auth) 에서 에러가 발생하지 않고 로직이 진행될 수 있다.  
  
물론 개발자가 아래와 같이 회원인지 검사하는 로직을 추가하면 되기는 한다  
```java
if(auth.isAuthenticated()) {
  memberService.do(auth);
}
```
  
하지만 어떤 초보 개발자가 이 작업을 하지 않는다면 문제가 생긴다.  
또 memberService 에서 이 로직을 구현해도 되지만  
controller를 구현한 개발자도, service를 구현한 개발자도 회원인지 검사하는 로직을 까먹는다면  
비회원인 AnonymousAuthenticationToken 으로 회원용 로직이 돌아갈 수 있다.  

<br><br>  
  
## 예시 2. 비회원 null 이 들어온다면  

앞선 예시와 다르게 비회원일때 Authetication 객체가 null 이 되어버린다면    
회원 검증 로직을 잘 못 구현하더라도   
<code>memberService.do(auth);</code> 에서 nullPointerException 을 발생시켜버린다.  
왜냐하면 auth에 . 점을 찍고 메서드나 필드를 호출하는 순간 바로 nullPointerException 을 발생시켜 버리면서  
개발자가 "이게 왜 null 이지?" 라고 의문을 품고  
비회원인 경우 null 이 들어오는 구나 라고 깨닫게 할 수 있기 때문이다.  
  
즉 로직이 실수로 진행될 위험을 원천적으로 막아버릴 수 있다.  
  
