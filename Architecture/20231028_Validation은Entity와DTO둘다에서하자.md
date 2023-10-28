[참고1](https://stackoverflow.com/questions/42280355/spring-rest-api-validation-should-be-in-dto-or-in-entity)  
[참고2](https://stackoverflow.com/questions/11929781/check-preconditions-in-controller-or-service-layer/12041382#12041382)  
[참고3](https://stackoverflow.com/questions/19100317/spring-dto-validation-in-service-or-controller)  
[참고4](https://chiki-cha.tistory.com/150)  

---
  
# Validation 은 어디에 둬야할까?  
Request 자체에 대해서 Validation을 할 수도 있고  
Entity 에서 Validation을 할 수도 있다.  
또 Service 계층에 따로 Validation Method 를 만들 수도 있다.  
  
그럼 어떻게 하는 게 좋을까?  
  
답은 둘 다 이다.  
  
<br><br>  
  
# 둘 다? 코드 중복 문제가 생기지 않나?  

단점이 **코드 중복** 으로 명확해서 조심스럽긴 하지만  
장점도 분명하다.  

### 단점 : 코드 중복  

### 장점  
1. Request DTO에서 검증하면, 성능적 이점이 있다   
2. Request DTO에서 검증하면, 웹 응답에 적합한 형태로 사용자에게 메세지를 반환할 수 있다   
3. Entity 에서 검증하면, DB에 들어가기 전에 철저히 검증된 데이터만 들어갈 수 있다. 특히 DTO를 거치지 않은 경우 Entity 에서 검증이 필요하다   
4. Request DTO와 Entity 에서 검증하면, Request 값 -> 로직으로 변환된 값 -> Entity 값 이렇게 변화하는 값에 대해서 각기 검증이 가능해진다. (ex. 비밀번호)    
> 비밀번호의 경우 Request 에서는 Raw String 이 들어오지만, Entity 에 들어가는 값은 PasswordEncoder에 의해서 생성된 Encoded String이 들어간다.  
   
- DTO: 사용자 입력에 대한 빠른 피드백과 초기 검증을 수행     
사용자에게 오류 메시지를 표시하고 데이터 수정 기회를 제공합니다.   
    
- Entity: 데이터의 일관성과 비즈니스 규칙을 유지  
시스템 오류로 간주되고 예외를 던집니다.  
  
<br><br>  

# 결론 : DTO / Entity 둘 다 Validation 이 좋음  
코드 중복이라는 단점이 있지만  
둘 다 Validation을 수행할 시 장점이 더 많기 때문에  
두 계층 모두 다 Validation 거치는 게 좋다.  
  
