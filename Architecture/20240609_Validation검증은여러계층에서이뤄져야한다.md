> ### 앞서 했던 Validation 위치에 대한 고민들
> [고민1-Entity/DTO둘다Validation](Architecture/20231028_Validation은Entity와DTO둘다에서하자.md)  
> [고민2-복잡한validation](Architecture/20231029_Validation복잡한경우처리방법.md)  
>   
> 앞서 고민1,2 를 통해서 검증에 대해 고민해봤지만 여전히 석연치 않았는데  
> 이번에 '축구 라이브 통계 프로젝트'를 만들면서 검증을 어떻게 둘지에 대한 생각이 정리된 것 같아 기록을 남긴다.
  
<br>  
  
---  
  
<br>
  
## 모든 계층에서 Validation 을 해도 된다  
  
Controller, Service, Domain 내부, Entity 모두 다 validation 을 해도 된다.  
  
## 왜? 값이 바뀔 때마다 검증을 해줘야 한다.  
  
예를 들어 "닉네임 규정" 비즈니스 로직이 있고, 닉네임 등록/변경 요청에 대해서 검증한다 해보자.      
  
controller dto 에 대해 검증한 다음 domain 으로 넘겨줬으면 끝났다고 생각할 수 있다.  
하지만 "닉네임 규정" 에 대한 검증은 예상치 못하게 "휴면 계정 해제" 과정에서도 발생할 수도 있고  
닉네임 규정이 변경된 이후에 전수조사에서 발생할 수도 있다.  
  
그래서 Controller request dto 에 닉네임이 존재하지 않고    
domain 내부에서 entity 를 가져온 뒤에 닉네임이 존재할 수 있다.    

따라서, 구체적인 비즈니스 로직을 담은   
애플리케이션 전반에서 여러 곳에서 사용될 법한 validation method 는   
Spring Bean 으로 등록하고 DI로 ValidationHelper 를 가져와서 사용하는 게 좋다.  
또한 Validation 로직은 바뀔 가능성이 매우 높으니까  
처음부터 DIP(의존관계역전원칙) 를 지키며 만들어 주는 게 좋을 것 같다.  

> ### DIP(Dependency Inversion Principal)
> 의존관계역전원칙.
> 구체적인 구현 클래스에 의존하는 게 아니라, 인터페이스나 AbstractClass 같은 추상 타입에 의존해야 한다.
> 그래야 변경에 용이함    
  
<br><br>  

## DI 를 사용하기 시작하면서, 맘놓고 여러 곳에서 validation 가능  
   
[참고 - Spring Rest API validation should be in DTO or in entity?](https://stackoverflow.com/questions/42280355/spring-rest-api-validation-should-be-in-dto-or-in-entity)  
  
의심되거나 값이 변경되는 경우에는 꼬박꼬박 validation 을 해주는 게 좋을 것 같다.  
애초에 Validation 이라는 게 "일관성" "통일성" 에 대한 부담 때문에 여러 계층에 두기 부담스러운데    
이를 DI 로 해소하여 여러 곳에서 validation 을 부담없이 해도 된다.  
  
또한 최소한으로 Request DTO 검증 / Entity 계층 검증 으로    
최전선과 최후방에서 각각 검증을 해주어 안정성을 더해주는 게 좋다.  
데이터의 in/out 의 관점에서 검증한다고 생각하면 좋다.  
  
