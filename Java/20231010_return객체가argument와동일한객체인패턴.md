# "Argument 객체 = Return 객체" 인 패턴   
  
```java
SomeType method(SomeType arg) {
  // ... 로직 ...
  return arg;
} 
```
이렇게 Argument로 들어온 객체가 어떤 로직을 거친 뒤 그대로 반환하게 되는 메서드를 자주 볼 수 있다.  
  
간단히는 Method Chaining 에 쓰겠거니 라고만 생각했는데  
Spring Security 소스 코드를 보다가  
Chaining 에 전혀 쓰이지 않는데도 이런 구조를 가지는 method를 많이 보게 됐다.  
그래서 위와 같은 패턴이 어떤 의도가 있는지 정리해봤다.   
    
> ### 이 패턴의 제대로 된 이름  
> 구체적으로 Method Chaining, Fluent interface, Method cascading 같이 다양하게 불린다.  
> 특히 Fluent Interface 라는 이름은 "유창하게" 코드가 읽힌다 라는 의미를 담고 있는 점이 재밌다.  
>   
> 그치만 이 문서를 읽는 사람이라면 아마도 Fluent interface 라는 명칭은 어색하고 Method Chaining은 혼동의 여지가 있고,  
> "인자 객체를 리턴하는 패턴" 이라고 적는게 한국인 개발자에게는 명확하게 이해되니까    
> 일단 이 문서에서는 "인자 객체를 리턴하는 패턴" 이라고 하겠다.   
  
<br><br>  
  
# 1. Method Chaining  
```java
// 반복되는 setter
Member member = new Member();
member.setAge(30);
member.setName("kim");
member.setRole("admin);
```

```java
// method chaining
Member member = new Member();
member.setAge(30).setName("kim").setRole("admin");
```

가장 쉽게 생각할 수 있는 의도는 Method Chaining 이다.    
이는 Builder 패턴으로도 자주 쓰인다  

```java 
Member member
  = Member.builder()
    .setAge(30)
    .setName("kim")
    .setRole("admin")
    .build();
```

특히 Builder 패턴을 통해서   
가독성, 유연한 생성 시점에 값 주입, setter를 열어두지 않아도 돼서 예기치 않은 setter 방지 등    
여러 이점을 얻어갈 수 있다.  
따라서 Method Chaining은 특히 이러한 "인자 객체를 리턴하는 패턴"의 핵심이기도 하다.  
  
<br>  

# 2. 확실한 의도 - "이 method는 인자로 들어온 객체의 값을 변경시킵니다"  
  
method가 쭈루루룩 길게 어떤 작업이 일어나더라도  
끝에 딱 argument와 동일한 객체가 return으로 박혀있으면  
"아. 어쨌거나 argument로 들어온 객체에 뭔 작업을 해서 반환하는 method 구나" 라는 것을 알 수 있다.  
  
여기서 추가적으로  
"해당 객체가 정상적으로 반환되고 어떤 값을이 채워졌다면, 그 method는 성공적으로 수행 됐음을 뜻합니다" 라는 의미도 담겨있다.  
  
<br><br><br>  

# 그럼 항상 "인자 객체를 리턴하는 패턴" 이 좋은가? - 아니다  
  
오히려 남용되면 가독성을 해칠 수 있다.  
즉, Chaining 으로 쓰면 더 가독성이 떨어지는 경우에는  
이런 방식이 좋지 않을 수 있다.  
  
## 1. method chaining 오남용으로 인한 가독성 문제  
```java
if(someMyInstance.loadSomeData().getSomeConditions(conditionParams).isValid()) {
  ...
}
```
  
if 조건 () 안에는 딱 심플하게 "그래서 어떤 조건인데" 만 들어가야 좋다  
그런데 위와 같이 과도한 chaining 이 이어지면  
오히려 1 line 에 N 개의 작업이 이뤄지므로  
가독성이 더 떨어지는 코드가 된다.  

즉, 때로는 void 타입으로 둬서 chaining을 막는 편이 더 가독성이 좋아질 수 있다.  
그래서 딱히 chaining이 필요가 없으면 return을 void로 해두는 것이 좋다. 그래야 chaining 오남용을 막을 수 있다.    
  
<br>  
  
## 2. chaining 디버깅 문제  
"chaining 은 너무 멋져!" 라고 생각해서 무작정 chaining을 유도하는 구조로 만들면  
오히려 강제된 chaining 때문에 debugging이 어려워질 수 있다.  
  
예를 들어   
  
```java
someMyInstance
  .loadData(targetData) // 메서드 1  
  .adjustWithCondition(conditionParams) // 메서드 2
  .processingData();  // 메서드 3
```

이러한 구조로 되어 있었는데 특정 케이스에서만 이상하게 동작하는 버그가 생겼다고 해보자.  
그러면 메서드 1,2,3 중에서 어느 지점에서 문제가 발생했는지 확인을 해야한다.  
그런데 위와 같이 method chaining이 된 경우에는 debugging이 어려워지게 된다.  

<br>  
  
# 결론. chaining 잘 알고 쓰자  
  
이와 같은 "인자 객체를 리턴하는 패턴"은 chaining을 만들 수 있지만  
반대로 chaining을 열어둬버린다는 점이 도리어 문제가 될 수 있음을 인식하자.  
 
하나의 메서드가 굵직굵직한 작업을 하는 경우에는 리턴을 void로 두는게 좋을 수도 있다.  
  
<br>  

# 이 글 쓰게 된 계기 - Spring Security 에서 이런 구조를 봤다  

```java
Authentication authenticate(Autentication autentication) { 
  ...
  return authentication;
}
```
간략히 보여주면 이런 구조로 되어있는 메서드들을 Spring Security 에서 많이 봤다.  
authenticate() 메서드는 엄청 굵직하고 의미가 큰, 로직상 거의 큰 줄기이자 핵심인 메서드인데   
이렇게 인자로 들어온 authentication 객체를 반환하는 모습을 보였다.  
  
그런데 Chaining 으로 쓰이기도 하지만  
이게 또 추상화된 상위 객체에서 타고 흘러가다 보면  
authenticate() 메서드를 보고 "뭐. 그냥 어떻게 잘 인증을 거친 객체가 반환 됬겠지?" 하고 가볍게 치고 넘어갈 수 있기도 하다.  
그래서 <code>someMethod(authenticate(authentication))</code> 이렇게 호출되어도 크게 가독성에 문제가 없는 상황일 수도 있다.    
