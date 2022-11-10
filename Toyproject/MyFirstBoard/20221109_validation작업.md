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




