### 아래와 같은 순서로 설명함  
**1. 구현 방법**  
**2. 원리** 
  
---  
  
# 1. 구현 방법 

### 1) 정적 자원 경로 생성   
  
<img width="218" alt="image" src="https://user-images.githubusercontent.com/101965836/235293968-189f7e0b-8347-4ebc-ad7f-eb307acdc120.png">  
  
resources/static/ 하위에  
/css 또는 /js 로 경로를 생성함   
  
### 2) Thymeleaf 에서 정적 파일 사용  

```html
<link th:href="@{/css/board.css}" rel="stylesheet">
```

a. th:href : thymeleaf 문법을 사용한 URL 지정 방식 사용  
b. @{...} : thymeleaf 에서 URL 을 적을 때 사용하는 문법   
  
> 위 경우 변수가 없으니 그냥 href="/css/board.css" 로 사용해도 된다    
> - 변수가 있는 예시  
> th:href="@{/image/user/{id}/profile.jpg(id=${id})}"  
  
---

# 2. 원리   
  
### 1) 절대경로 상대경로  
  
절대경로와 상대경로는 흔히 아는대로   
/image/... 같이 지정하면 root 부터 시작한 경로 지정이고  
image/... 같이 지정하면 현재 경로(상대 경로) 부터 시작한 경로 지정이다
  
근데 문제는  
root이 resources/static 으로 인식되는데  
이건 어떻게 동작해서 root이 저기로 인식되냐는 문제이다  
  
이에 대한 답은 spring의 정적파일 처리 있다  
    
### 2) Spring 의 정적 파일 처리  
Spring에서 정적 파일 요청이 들어오면     
"기본 static 파일 경로 후보들" 을 탐색해서  
지정된 정적 파일을 찾는다    
  
정적 파일이 있는 후보 경로는 다음과 같다  
```
/static
/public
/resources
/META-INF/resources
```
  
> ### [Reference](https://docs.spring.io/spring-boot/docs/3.0.6/reference/htmlsingle/#web.servlet.spring-mvc.static-content)   
> By default, Spring Boot serves static content   
> from a directory called /static (or /public or /resources or /META-INF/resources)   
> in the classpath or from the root of the ServletContext.   
  
위 경로들을 root로 두고 정적 파일들을 찾아서 반환해준다  
  
