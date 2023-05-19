# ---작성중---  
[참고1 - static classes in java](https://stackoverflow.com/questions/7486012/static-classes-in-java)   
[참고2 - why cant we have static outer classes](https://stackoverflow.com/questions/18036458/why-cant-we-have-static-outer-classes)   
[참고3 - JVM 내부 구조 & 메모리 영역](https://inpa.tistory.com/entry/JAVA-%E2%98%95-JVM-%EB%82%B4%EB%B6%80-%EA%B5%AC%EC%A1%B0-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%98%81%EC%97%AD-%EC%8B%AC%ED%99%94%ED%8E%B8)  
[참고4 - java inner class and static nested class](https://stackoverflow.com/questions/70324/java-inner-class-and-static-nested-class)    
   
<br><br>  
  
# static 클래스  

static 메서드, static 필드는 어느정도 감이 잡히는데  
**static 클래스**는 감이 안잡힌다.    
  
- static 필드 : 객체들에서 통일된 값을 유지해야 할 때 사용한다.     
- static 메서드 : 객체 생성 없이 호출되어야 하는 경우    
  
> ### static 메서드  
> 1. 유틸리티, 헬퍼  
> 2. Factory 메서드  
> 3. 성능최적화 (호출이 잦지만 객체 생성이 필요 없는 경우)  
> 4. 상태가 없는 메서드 (메서드가 입력된 인자에만 의존)    
  
<br><br>  

---

<br><br>  
   
# static 클래스의 특징  
> class에 붙은 static은 뭔가?    
내가 정리한 **static 클래스의 특징**은 이렇다.   

1. (Nested) 해당 클래스가 속한 클래스의 인스턴스와 무관하게 동작하도록 한다.    
2. (Instantiate) new 키워드로 인스턴스 만들 수 있다. 
3. (Method) Static 처럼 접근하면 static method만 호출 가능. Instance에서 접근하면 non-static method만 호출 가능.    
  
여기서 나의 가장 큰 오해가 2번이었다.  
static 변수는 모든 객체에서 같은 값을 가지니까  
static class도 마치 모든 객체가 같은 값을 가지는 것처럼 생각했고  
그래서 인스턴스를 만들 수 없다고 생각했다  
그런데 코드로 직접 내가 테스트해본 결과는  
static class로 인스턴스를 만들 수 있으며  
일반 class와 동일하게 동작하고,    
단지 class 접근과 관련된 것 뿐이다.  
  
  
# static class를 쓰는 이유
  
> static class는 Nested Class 만 가능하다.  
   
1. 네임 스페이스 오염을 막으면서 별도 클래스를 선언 가능     
2. 밀접하게 outer class와 관련있게 선언 가능   
3. OuterClass 생성 없이도 Nested class 를 생성 가능하다.  

<br>
   
## inner static class만 가능   
outer static class는 없다.  
  
```java
// 파일명 : OuterStaticClass.java
  
public static class OuterStaticClass {  // static이 안된다고 에러가 난다  
  ...
}
```
    
### 왜 inner는 안되는거지?   
  
> There are no outer static classes in Java.    
> Because all outer classes are already visible    
> like the static modifier would do.     
> [출처](https://stackoverflow.com/questions/18036458/why-cant-we-have-static-outer-classes)   
    
static이 붙으면 뭐가 달라지는가에 대해서    
**접근** 관점에서 보면    
1. class만 볼 수 있어도 접근 가능한가?  
2. 객체를 만들어야 접근 가능한가?  
이렇게 나뉜다.  
   
### "접근 가능한가" 가 무슨 말인가  
아래 코드를 보자.   
   
```java
SomeClass.accessStatic(); // class만 볼 수 있어도 접근 가능  

SomeClass instance = new SomeClass();
instance.accessNonStatic();  // 객체를 만들어야 접근 가능  
```
    
visible이라는 말은 namespace에서 인식 되는가이다.    
namespace 인식 예시는 간단히 말하면   
import 같은 경우도 있고   
somepackage.someclass.somefield 같이 접근하는 것도 있다.  
   
### 어떻게 static이라는 키워드를 이해할 것인가  
  
"직관적으로 static을 어떻게 이해할 것인가?"   
나는 단 하나의 직관적인 정리를 만들 수 없어서,    
대신 아래와 같이 몇 가지로 나눠서 이해하고 있다.    
  
1. static은 모두가 공통된 값을 쓰기 위한 키워드다 (field)   
2. static은 객체 없이, 객체보다 앞서서 사용하려 할 때 쓴다 (method)  
3. static은 클래스와 관련있지만, 객체의 상태와는 상관없을 때 사용한다 (method, class)
  
여기서 3번이 바로 static class의 사용처가 된다.   
  
  
