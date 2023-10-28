> "Validation 을 어디에 둘 것인가?"  
> "Validation Error를 어떻게 처리 할 것인가?"  
> 에 대해서 고민한 내용을 정리한 글 
  
<br><br>  
  
# Validation 위치 "3곳" 
  
1. Controller(또는 DTO) 에서 Annotation 을 통한 Validation
2. Service Layer 에서 Java Code를 통한 Validation
3. Entity 에서 Annotation 및 Java Code 를 통한 Validation
  
<br>

# Entity 에서 Validation 은 필수  
  
### 이유 : DB에 들어가기 전 데이터 무결성과 일관성을 위한 검증이 중요하다    
  
예를 들어 제일 앞쪽인 RequestDTO 만 검증한다고 해보자.    
그러면 Controller - Service - Repository 계층 구조에서 Controller 에서만 검증이 일어난 셈이다.     
  
그런데 Service 에서 값을 다루던 중 어떤 문제가 생겨서   
빈 데이터가 넘어가거나, 서비스 요구사항에 알맞지 않은 데이터가 들어갈 수 있다.  
따라서 DB에 값을 넣기 전에 최종 점검 차원에서 Entity 에 대한 Validation이 필요하다.  
  
그리고 단위 테스트 관점에서도 Entity 에 대한 Validation 이 중요하기도 하다.  
Controller 에만 Validation 이 있으면, Contoller 계층 없이 실시한 단위 테스트에서 발생한 Validation Error 를 감지하지 못할 수 있다.  
따라서 Entity 에 Validation 을 둬서 최종적으로 검증하는 것이 좋다.  
  
<br>  
  
# Controller 에 Validation 을 두는 게 심플해 보인다  
  
앞서 "Entity 에서 Validation 은 필수" 인 이유에 따라서   
Validation 3곳 중 Entity 는 필수임을 알았다.   
    
그러면 DTO 와 Service 중에서 어디에 Validation을 두는 게 좋을까?    
아니면 DTO, Service, Entity 3곳에 모두 Validation을 둬도 될까?    

이에 대한 내 결론은    
### Client 관련 Validation 이니 Controller 에 두는 게 좋을 것 같다.  
    
<br>     
   
# Validation 이 복잡해지면, 별도로 Validation Helper 를 만들고 주입받아서 사용하자  
간단한 검증은 Annotation Validation  
복잡한 검증은 Helper 를 통한 Validation 을 추가로 해주면 된다.  
  
복잡한 검증을 담당하는 Helper 는 BindingResult 객체를 받아서 에러가 있으면 추가해준다   
   
```java
@Component
public class SignupValidationHelper {
  public void validate(RequestDto dto, BindingResult bindingResult) { 
    // when invalid
    if(dto.getPassword().length() < 6) {
      bindingResult.rejectValue("password", "length", "비밀번호는 6자 이상이어야 합니다.");
    }
  }
} 
```  
   
Controller 에서는 Helper 에게 넘긴 BindingResult 에서 hasError() 를 통해서 검증 결과 Error 가 있는지를 확인하도록 한다.   
```java
@Autowired
private SignupValidationHelper signupValidationHelper;

@PostMapping("/signup")
public String signup(@Valid SignupDto signupDto, BindingResult bindingResult) {

    signupValidationHelper.validate(signupDto, bindingResult);

    // - Invalid
    if (bindingResult.hasErrors()) {
        return "form";
    }

    // - Valid
    // 등록 로직
    return "redirect:/home";
}
```
  
> ### 참고한 글 [Validation in Spring MVC in Controllers or Service Layer?](https://stackoverflow.com/questions/21407840/validation-in-spring-mvc-in-controllers-or-service-layer)   
> In one of our previous projects, we had huge forms with very complex logic which meant a lot of validating code. So we used a third kind of solution. For every controller, we autowired a helper class. Example:   
>    
> ```  
> myview <-> MyController <- MyService <- MyDAO
>                 ^
>                 |
>               MyHelper
> ```  
> Controllers handled the view resolving.    
> Services handled mapping from dto-s to model objects for view and vice versa,    
> DAO-s handled database transactions and,    
> Helpers handled everything else including validation.   
>    
> If now someone would have wanted to change the frontend from web to something else, it would have been a lot easier and at the same time, we didn't over-bloat the service implementation classes.   
