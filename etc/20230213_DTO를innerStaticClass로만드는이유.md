[출처](https://jaehoney.tistory.com/157)  
  
# DTO 문제점 : 너무 DTO가 많아진다  
![image](https://user-images.githubusercontent.com/101965836/218442338-5efdd917-27b9-4684-8113-98b889897f6b.png)  
  
1. DTO를 따로 클래스를 빼놓으면 너무 DTO 클래스가 많아진다.  
2. 어디에 어떤 DTO가 쓰이는지 한눈에 안들어와서 유지보수가 힘들다.    
  
<br><br>  
  
# 대안 : Domain 마다 DTO 디렉토리 따로 파서 넣기  
![image](https://user-images.githubusercontent.com/101965836/218456263-6caf6c84-4426-4046-8bbb-0aca1392a00f.png)  
  
Domain 안에 DTO 패키지를 따로 파서 관리하지만  
여전히 DTO가 관련없는 다른 domain 에서도 노출된다는 점에서 문제가 있다  
  
<br><br>
  
# 더 좋은 대안 : Inner Static Class 로 만들기  
  
```java
@Controller 
public class MemberController {

  ...
  
  @Data
  static class MemberDto {
    private Long memberId;
    private String name;
    private LocalDateTime lastLogin;
    
    public SimpleOrderDto(Member member) {
      this.memberId = member.getMemberId();
      this.name = member.getName();
      this.lastLogin = member.getLastLogin();
    }
  }
}
```
   
위와 같이 Controller 에서 DTO를 InnerClass로 두면  
1. DTO 클래스가 많아지는 문제 -> InnerClass로 숨길 수 있음  
2. 어떤 DTO가 쓰이는지 한눈에 안들어오는 문제 -> Inner Class 로 뒀기 때문에, 해당 DTO는 해당 Class 에서만 씀을 바로 알 수 있음  
  
어?  
근데 왜 STATIC 이지??  
  
<br><br>  
[참고1](https://yuja-kong.tistory.com/entry/Java-inner-class-%EC%99%80-inner-static-class-%EC%B0%A8%EC%9D%B4)  
[참고2](https://my-codinglog.tistory.com/m/entry/Nested-Class-%EC%97%90-%EC%96%B8%EC%A0%9C-static-%EC%9D%84-%EB%B6%99%EC%97%AC%EC%95%BC-%ED%95%A0%EA%B9%8C)  
[참고3](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9E%90%EB%B0%94%EC%9D%98-%EB%82%B4%EB%B6%80-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%8A%94-static-%EC%9C%BC%EB%A1%9C-%EC%84%A0%EC%96%B8%ED%95%98%EC%9E%90)  
  
# 반대로 생각하자. static 없는 Inner class 에 문제가 있다.  
  
1. 외부 참조 문제  
2. 메모리 누수 현상    
  
### 1. 외부 참조 문제  
inner는 static , outer class는 non-static 이므로  
Static 초기화 시점에 non-static에 접근 못하니까  
DTO가 Controller를 참조하지 않도록 할 수 있다.  
  
반면 그냥 inner class 로 선언해버리면   
outer 를 만든 다음 inner 를 만들어야 한다.
  
그럼 생성시점에만 outer 참조하는 거 아니냐?  
라고 말 할 수 있는데  
실제로 컴파일된 .class 파일을 보면  
inner의 생성자에서 outer class 를 자동을 받게된다.  
  
```java
class Outer$Inner {
  int inner_field;
  
  Outer$Inner(Outer this$0) {
    this.this$0 = this$0;
    this.inner_field = 20;  
  }
}
```
class 파일을 보면 위와 같이 Outer 를 생성자에서 자동으로 파라미터로 받게 되며,  
Inner 안에 필드로 Outer 참조를 갖고있게 된다.  
따라서 non-static Inner class 는 어쩔 수 없이 Outer 참조를 계속 갖고 있게 된다.  
  
<br><br>
  
### 2. 메모리 누수 현상    
  
앞선 외부 참조 문제로부터 이어지는데  
Outer 를 참조로 갖고 있게 되니까  
안쓰는데도 Outer Class 를 통째로 갖고 있어야 한다.  
  
따라서 Inner Class만 쓰는데 Outer Class 까지 다 갖고 있어야 하므로 메모리 낭비가 된다.  
  
<br><br>  
  
---
  
<br><br>  
  
# 결론: DTO는 controller 안에 Static Inner Class 로 만들자  

```java
@Controller 
public class MemberController {

  ...
  
  @Data
  static class MemberDto {
    private String name;
    
    public SimpleOrderDto(Member member) {
      this.name = member.getName();
    }
  }
}
```
  
### 이점
1. 내부 클래스로 DTO 많이 생겨서 복잡해지는 문제 해결    
2. static 달아서 메모리 누수 문제  
