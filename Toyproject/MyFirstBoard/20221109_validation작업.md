- Validation 작업 정리  
validation 작업은 빈번하게 일어나는데, validator를 쓰거나 annotation으로 간단히 해결할 수 있기도 한다  
주로 간단한 null또는공백금지, 최대값 최소값 같은 경우 annotation을 쓰고  
좀 더 복잡한 로직이 필요한 경우 Spring이 제공하는 validator를 써서 AOP 방식으로 해결할 수 있다  
그리고 이 과정에서 bindingResult에 error가 알아서 담기고  
에러 메세지 또한 errorcode에 따라서 적절히 생성된다.  
  
빈번하게 일어나는 validation에 대해서   
  
1. validation 방식 정리  
2. error 메세지 전달, 메세지 코드 정리  
3. thymeleaf 에서 표시하는 방법 정리  
  
위 3가지로 나눠 정리하겠다  

--- 
  
<br>  
  

# 1. Validation 방식  
  
## a. Validator  
지금까지 프로젝트에서 썼던 방식이다  
  
### Validator 구현 방법  
```java
@Component // Spring Bean 등록해준다
public class PostWriteValidator implements Validator {

  @Override
  public boolean supports(Class<?> clazz) {
    return PostWriteDTO.class.isAssignableFrom(clazz);
  }
  
  @Override
  public void validate(Object target, Errors errors) {
    
    // validation 로직을 여기에
    ValidationUtils
      .rejectIfEmptyOrWhitespace(errors, "writer", "required", "필수 값입니다.");   
      // (Errors , 필드 , 에러코드, 디폴트 메세지)
  }
}
```

위와 같이 먼저 Validator를 만들어 준다.  
Validator 인터페이스를 구현해서 두 메서드를 만들어주면 된다  
  
1. supports(Class\<?> clazz)    
매개변수로 넘어온 clazz 를 지원하는 Validator 인가를 판단하는 메서드다  
  
2. validate(Object target, Errors errors)  
target으로 validation 작업할 객체가 넘어오고   
검사 결과 에러가 있으면 errors 객체에다가 에러를 담아주면 된다.  
위 예시코드에는 ValidationUtils를 사용했는데   
직접 errors에 에러메세지를 담을려면 errors.rejectValue() 나 errors.reject() 를 쓰면 된다  
  
### Validator 컨트롤러에 적용  
```java
@Controller
public class boardController {

  ...
  // 1. Validator를 먼저 생성해 준다.
  private final PostWriteValidator postWriteValidator;
  
  // 2. Validator 등록  
  //  사용할 Validator를 InitBinder를 통해 등록해준다   
  @InitBinder("postWrite") // "postWrite"를 적어주지 않으면, 해당 컨트롤러 내에 다른 바인딩 작업에서 영향을 미친다  
  public void initBoardValidator(WebDataBinder webDataBinder) {
    webDataBinder.addValidators(postWriteValidator);
  }
  
  ...
  
  // 3. 컨트롤러 부분 작성법
  // 바인딩할 객체에 @Validated 애노테이션을 붙여주고, BindingResult 객체를 바로 다음 파라미터로 붙여준다  
  @PostMapping("/board/write")
  public String writePost(
          @Validated @ModelAttribute PostWrite postWrite,
          BindingResult bindingResult) {
    ...   
  }
  
}
```
  
컨트롤러에서 위와 같이 사용하면 된다   
주석을 달아놓은 대로   
  
1. Validator 객체 생성   
2. Validator 등록   
3. 대상 DTO에 @Validated 애노테이션 붙이기  
  
3가지를 해주면 된다  
  
> ## 사용시 주의사항  
> 1. @InitBinder("대상객체")  
> 위 예시코드에서 보듯 @InitBinder("postWrite") 같은 식으로 대상이 되는 객체를 지정해줄 수 있다  
> 명시적으로 지정해주면 postWrite만 딱 validator가 적용되고, Integer 같은 타입 바인딩은 validator가 작동안한다    
> 만약 지정안해줬는데 postWrite 바인딩이랑 Integer 바인딩이 동일 컨트롤러 내에서 동시에 일어나면  
> 에러가 난다  

  
### 장점
1. 코드로 Validation 작성 가능 -> 로직 이해 쉬움, 상세한 로직 설정 가능
  
### 단점  
1. 코드 작업량이 많음  

<br><br><br>  

## b. Annotation 
form 데이터를 받아올 DTO 클래스를 선언할 때   
\@Max(10) 같이 Annotation을 붙여줘서 Validation 작업을 정해준다   
  
```java
@Length(min = 3, max = 15)
private String lastName;
``` 
  
정확히는 **"Bean Validation"** 이라고 한다  
  
### 장점 
1. 정말 간단하게 Validation 작업을 할 수 있다  

### 단점  
1. 조금만 복잡해지면 사용하기 애매해진다.   
2. (근데, 실제로는 대부분 공백 불가 같은 간단한 조건들이여서 Bean Validation으로 해결 가능하다)  
  
<br><br>  
   
> ## 참고로 Bean Validation 은 기술 표준이다   
> Bean Validation 은 Bean Validation 2.0(JSR-380) 이라는 기술 표준이다.  
> 기술 표준이란, 이렇게 이렇게 만드세요 하고 기준이 정해지면  
> 그에 맞춰 구현된 구현체들이 여럿 있고, 그 중에 선택해서 쓰는 것을 말한다  
> 구현체로는 Jakarta Bean Validation, Hibernate Bean Validation 등이 있다.  
> (여기서 Hibernate는 ORM Hibernate와 이름은 같지만 전혀 다른놈이다  
    
<br><br><br>  

---






