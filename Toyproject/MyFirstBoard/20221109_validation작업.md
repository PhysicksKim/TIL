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
  
> # 사용시 주의사항  
> ## 1. @InitBinder("대상객체")  
> 예시에서는 @InitBinder("postWrite") 같은 식으로 대상 객체를 지정했다  
>   
> 만약, 컨트롤러에 Validator가 동작할법한 대상으로는 postWrite만 있다면     
> 대상 객체를 지정안해주고 @InitBinder 라고만 붙여줘도 된다  
>   
> 하지만 postWrite 말고 다른 객체도 Model에 담겨서 Validator가 동작한다면 에러가 난다  
>   
> ### 에러 예시
> ```java
> 
> @InitBinder
> public void initValidator(WebDataBinder webDataBinder) { 
>   webDataBinder.addValidators(postWriteValidator);
> }  
>   
> @GetMapping("/board/free")
> public String boardList(@RequestParam Integer page, Model model) { 
>   model.addAttribute("pagination", pagination);
>   ...
> }
> 
> @PostMapping("/board/write")
> public String writePost(@Validated @ModelAttribute PostWrite postWrite,
>                 BindingResult bindingResult) { ... }
> ```
> 이렇게 @InitBinder 에 대상 객체를 지정 안해줬는데  
> 위에서 보면 boarList 에서 model 에다가 pagination 이라는 객체를 넣어줬고  
> 아래의 writePost() 에서도 @ModelAttribute 를 통해 자동으로 model 에다가 postWrite 객체를 넣어줬다  
>   
> 이러면 pagination 객체가 model에 담겨 넘어갈 때 validator 가 있는지 찾아서 검사하게 되는데 
> postWrite용 validator만 있으므로 pagination용 validator가 없어서 에러가 난다    
>  
> 따라서 여러 객체들이 model에 담기고 한다면  
> @InitBinder("postWrite") 같은 식으로 대상이 되는 객체를 지정해준다  
>  
> [참고한 글](https://www.inflearn.com/questions/280541)  
>    
>    
> # 2. BindingResult bindingResult 위치  
> ```java  
> public String writePost(@Validated @ModelAttribute PostWrite postWrite,
>                 BindingResult bindingResult) { ... }
> ```  
> 이런식으로 @Validated 달린 파라미터 다음 위치에다가 BindingResult를 넣어줘야 한다  
> 
  
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

- 근데, 실상은 대부분 '공백 불가' 같은 간단한 조건들이여서 Bean Validation으로 해결 가능하다  
  
<br><br>  
   
> ## 참고로 Bean Validation 은 기술 표준이다   
> Bean Validation 은 Bean Validation 2.0(JSR-380) 이라는 기술 표준이다.  
> 기술 표준이란, 이렇게 이렇게 만드세요 하고 기준이 정해지면  
> 그에 맞춰 구현된 구현체들이 여럿 있고, 그 중에 선택해서 쓰는 것을 말한다  
> 구현체로는 Jakarta Bean Validation, Hibernate Bean Validation 등이 있다.  
> (여기서 Hibernate는 ORM Hibernate와 이름은 같지만 전혀 다른놈이다  
    
<br><br><br>  

---

# 2. error 메세지 전달, 메세지 코드  
  
![image](https://user-images.githubusercontent.com/101965836/201462366-63a3392c-e111-472b-aa74-21de9c84c0b8.png)  
  
validation 과정에서 error가 발생했으면  
알맞은 error를 model에 담아서 넘겨야 한다  
  
그러면 error를 담아서 넘기는 과정은  
  
1) error들을 Model에 담기
2) Model에는 어떤 error message들이 담길것인가  
  
이렇게 두 과정을 이해해야한다  

<br><br>

## 1) error 들을 Model에 담기  

### 사실, 자동으로 담긴다.

간단히 생각하면   
컨트롤러 파라미터로 Model model 해서 model을 가져오고  
model.addattribute(...) 하는 식으로 error를 담는다고 생각할 수 있다   
  
하지만 validator나 @Annotation을 쓴다면   
직접 model에 error를 담아줄 필요가 없다.    
BindingResult에 자동으로 error들이 담겨서 model로 넘어간다.  
  
```java
@PostMapping("/add")
public String addItemV1(@ModelAttribute Item item, 
                      BindingResult bindingResult,
                      ... )
```
이렇게 Item 객체 바인딩에 error가 있으면,  
bindingResult에 자동으로 error가 담겨서 model로 넘어간다  
  
> ### ❗ BindingResult는 바인딩 대상 파라미터 바로 다음에 와야한다  
> 위 처럼 (@ModelAttribute Item item , BindingResult bindingResult , ...) 같이 바인딩 대상이 되는 객체 바로 다음에 와야한다  
  
### Validator 는 Errors에 직접 담아줬기에 controller 에서는 신경안써도 된다    
```java
@Override
public void validate(Object target, Errors errors) {
  ...
}
```
검증 대상 객체인 target 과  
검증 후 error가 발생하면 reject 메세지를 담을 errors 가 넘어온다.  
  
짐작이 가겠지만  
validate() 메서드의 errors 객체가 자동으로 model에 담기므로,  
validate() 메서드 안에서 errors에 에러메세지만 잘 담아주기면 하면 된다.   

<br><br>

## 2) Model에는 어떤 error message들이 담길것인가  
  
> ### 요약 
> 
> 1. Errors 객체에다가 reject() 같은 메서드로 에러를 등록한다. 
> (validator가 자동으로 등록하거나 errors.rejectValue() 수동으로 등록할 수 있다)  
>   
> 2. errors.properties 파일에 아래와 같은 형식으로 error message들을 적어둔다.  
> errorcode.objectName.field=메세지형식    
>   
> 3. 매칭된 메세지가 Model에 담긴다  
> 앞서 errors.properties에 설정해둔 errorMessage가 String 형태로 Model에 담긴다.  
> 템플릿 엔진에 따라서 적절한 형태로 error를 출력해주면 된다.    
  
  
[errors Spring Docs](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/validation/Errors.html)  
```
rejectValue(String field, String errorCode)
```
rejectValue는 최소 위와 같이 field와 errorCode 를 파라미터로 받는다   
    
field와 errorCode, 그리고 바인딩 에러가 난 ObjectName 을 가지고  
errors.properties 파일에서 매칭되는 에러메세지를 찾는다  
  
<br><br>  
  
> ### 예시  
>   
> - errors.properties  
>   
> ```
> NotMatchName.ItemOrder.name=상품 이름을 잘못 입력했습니다
> ```
>  
> - BindingResult에 ObjectError 객체 등록  
>   
> ```java
> public String exampleController(
>       @ModelAttribute ItemOrder itemOrder, 
>       BindingResult bindingResult, ...)
>   // 컨트롤러 로직
>   bindingResult.addError(new FieldError("ItemOrder", "name" , ...));
> }
> 
> // bindingReulst.addError(ObjectError objectError) 메서드는 ObjectError 객체를 매개변수로 받는데  
> // 위에서 FieldError로 넣어줬다  
> // 이건 별거 아니고, 그냥 FieldError 클래스가 ObjectError의 자식이라서 저렇게 넣어도 된다  
> ```

[BindingResult Spring Docs](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/validation/BindingResult.html)  
[FieldError Spring Docs](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/validation/FieldError.html)  
  
1. bindingResult 에 담긴 에러는 컨트롤러가 끝나면 에러 메세지로 전환되어 자동으로 model에 담긴다   
2. Spring에서 정의한 FieldError ObjectError 객체 형태로 bindingResult.addError() 에다가 담는다   
3. FieldError ObjectError 객체는 위에서 보듯 FieldError(ObjectName , FieldName , ... ) 같이 ObjectName 과 FieldName 을 생성자에서 받아서 에러 코드를 적절히 만들어낸다  
  
<br><br><br>  

지금까지 어떤 과정을 거쳐서 에러 메세지가 model에 담기는지 알아봤다.  
  

---

<br><br><br>  

# 3. thymeleaf 에서 표시하는 방법

간단하다  
```html
<input ... th:errors="${postWrite.title}" >
```
th:errors 을 사용하면 error가 발생하면 해당 필드에 맞는 에러 메세지를 출력해준다  
여기서 출력하는 에러 메세지는  

```
1. model에 errorCode 담김 
2. errorCode 를 errors.properties 에 적어둔 에러메세지와 맵핑해서 적절한 메시지 채택
3. 채택된 에러 메세지를 템플릿 엔진이 표시  
```

이런 과정을 거치게 된다  
  
> 여기서 정확히 2. 3. 중 어디부터 template 엔진의 영역인지 모르겠다  
> errorCode 가 model에 담겨서 view로 넘겨지는지,  
> 에러메시지가 model에 String으로 담겨서 view로 넘어가는지를 정확히 모르겠다.     
> 그치만 일단은 중요한 부분이 아니니 넘어가자.  
  
<br><br><br>  
  
## 좀 더 잘 활용하기  

```html
<form name="postWriteForm" id="postWriteForm"
      th:action th:object="${postWrite}" method="post">
...
<table th:class="table">
  ...
  
  <tr>
      <td th:text="'작성자'"></td>
      <td> <input id="writer" name="writer" type="text"
                  placeholder="작성자" th:field="*{writer}">
          <div th:errors="*{writer}" th:errorclass="|error-text|">
              wrtier 오류
          </div>
      </td>
  </tr>
  
  ...
</table>
...
</form>
```

현재 토이프로젝트에서 글 작성 view의 일부분이다.  

1. form 에서 th:object 로 객체를 지정해준다  
2. input 부분에서 th:field 로 해당하는 필드를 지정해줬다. 필드를 지정해주면, 이전에 잘못 입력했던 값을 다시 input 안에다가 넣어준다    
3. 따로 div 빼서 th:errors를 출력하도록 했다. 만약 error가 없으면 해당 태그(div)는 출력되지 않는다  
4. th:errorclass 를 통해서 에러가 있으면 class를 append 해준다(사실 여기서는 에러용 div 태그를 따로 빼서 append 방식으로 class 추가할 필요는 없다)  
  
<br><br>  
  
## 앞의 2. 부분을 다시 설명하면  
  
>  2. input 부분에서 th:field 로 해당하는 필드를 지정해줬다. 필드를 지정해주면, 이전에 잘못 입력했던 값을 다시 input 안에다가 넣어준다    
  
th:field는 객체의 필드 값을 태그에 따라 적절한 형태로 맞춰 값을 넣어주는 타임리프 속성이다  
여기서 "그냥 th:errors만 쓰면 되는거 아니냐" 할 수 있어서 좀 더 자세히 설명을 적어본다.  
  
  <br>  
  
### a. 먼저 th:field는 th:object 랑 세트로 쓰인다.  
예를 들어  
```
th:text="${postWrite.title}"
```
이렇게 일일이 쓰기 번거로우니까  
```
<Form ... th:object="${postWrite}" >
  
  <input ... th:field="*{title}" >
```
이렇게 객체 지정을 큰 태그에서 한방에 하고, 필드 지정을 각각 자식 태그에서 해주는 거다.    
  
<br>  

### b. 에러시 입력했던 값을 다시 보여주기 위해서 th:field를 써줬다  
흔히들 이런 경험이 있을텐데  
  
뭐라뭐라 썼는데, "유효하지 않은 값입니다" 하고 다시 이전페이지로 돌아가서  
한참동안 쓴 내용이 다 사라지고 전부 다시 써야하는 일을 겪었을거다  
  
또 그게 잘못된 값이라 하더라도  
사용자가 "내가 뭘 잘못 입력한거지" 하고 이전에 입력한 값을 봐야지 딱 피드백이 잘 된다.  
  
예를들어 게시판 글 작성자에는 특수문자(?!@)를 입력할 수 없다는 조건이 있다고 해보자.   
사용자는 "**오렌지를먹은지얼마나오렌지!**" 라고 작성자 칸에다가 꿀잼 드립을 적어놨는데  
알고보니 특수문자는 사용할 수 없어서 반려된 상황이다.  
  
그러면 위 입력 값이 잘못됐더라도 그대로 살려놔야지   
**"아 내가 마지막에 !를 썼구나, 이거만 지우면 되겠네"** 라는 피드백이 제대로 올 수 있고  
더불어서 사용자가 글을 쓰는 사이에 작성자를 뭐라고 적었는지 다시 생각해내야하는 고통도 줄일 수 있다.  
  
따라서 에러가 발생했더라도 입력했던 값을 보존해두는게 중요하다   
  
<br><br>  

이를 위해 컨트롤러에서 입력 값을 받을때 
```java
public string postWriteController(
    @Validated @ModelAttribute PostWrite postWrite, 
    ...) 
```
이렇게 바인딩 시도한 객체를 곧바로 @ModelAttribute로 Model에 담았다.  
  
이렇게 Model에 에러난 객체더라도 값을 담아뒀기에 
th:object 와 th:field로 값을 다시 채워넣을 수 있는 것이다.  
  
  
---  

# 끝
