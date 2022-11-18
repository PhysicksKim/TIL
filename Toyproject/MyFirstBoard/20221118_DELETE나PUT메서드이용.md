글은 크게 둘로 나뉜다  
1. 이론) HTML Form 에서는 왜 Get과 Post만 지원하는가?  
2. 사용) Spring Thymeleaf 에서 Form Method로 Delete 사용하기
  
---  

# 1. 이론) HTML Form 에서는 왜 Get과 Post만 지원하는가?  

---

# HTTP Method
http method에는 Get Post 말고도 Put Delete Patch 같은 메서드들도 있다   
앞서 말한 주요 5개의 메서드 외에도 기타 4가지 메서드들이 더 있다 (Option Head Connect Trace)  
  
근데 여기서 오해하기 좋은 부분이  
**HTML Form** 에서는 Get 과 Post만 지원한다  
  
그렇다. HTML 에서는 JS 를 쓰지 않고는 Get 과 Post 만 사용 가능하다.  

<br><br><br>  

# 왜 Get 과 Post 만 가능?  
  
[참고 0](http://haah.kr/2017/05/23/rest-http-method-in-html-form/)  
[참고 1](https://mangkyu.tistory.com/218)    
[참고 2](https://medium.com/@carlospineda/why-no-methods-for-put-delete-in-html-f483b66d8874)   
  
위 글들을 토대로 내린 결론은 아래와 같다  
   
### Q. 왜 Get과 Post 만 가능한가?    
### A. 결국 Get Post 이외 메서드들은, Get 과 Post 의 하위 메서드일 뿐이다.  
  
Delete Put 같은 메서드들의 동작은  
결국 Post 메서드로 모두 동일하게 동작하도록 만들 수 있다  
단지 **'무엇을 하려고 하는가?'** 라는 의미만 추가적으로 담겨있을 뿐이다.  
 
 <br><br><br>  
  
# Form Method 추가시 장단점  
  
### 장점   
- 더 RESTful 하게 구현할 수 있다. 이 요청이 왜 들어왔는지 알릴 수 있다   
  
RESTful 한 설계로 구성한다면   
url은 resource 정보만 담고,  
Method가 해당 요청이 무슨 목적으로 들어왔는지 뜻해야 한다.     
  
예를들어  
```
/board/10 [GET] ; 10 번 글 읽기
/board/10 [DELETE] ; 10 번 글 삭제
/board/10 [POST 또는 PUT] ; 10 번 글 수정
```
이런 식으로 10번 글이라는 리소스만 딱 URL로 지정하고  
메서드를 통해서 구체적으로 어떤 동작을 할 지 구분지어주는게 RESTful 하다고 할 수 있다  
   
   <br>  
   
### 단점  
- 어차피 똑같이 동작하는데, Form Method 를 추가 할 필요가 없다  
  
어쩌면 legacy를 고려해서 form method를 추가해서 발생하는 문제를 줄이자는 생각일 수 있다.  
  
아니면 어차피 기타 메서드들은 get, post의 하위 차원이니까  
그냥 RESTful 하지 않더라도 URL을 좀 붙여서 알아서 해라 라고 부담을 URL 설계에다가 넘겨버린 셈일 수 있다.  
  
어느쪽이건 결국 Form Method 를 구태여 추가하다가 문제가 터지거나 불필요하게 스펙이 늘어날 바에는  
그냥 Form Method 가 추가로 필요하면 각종 엔진들이 알아서 구현하고  
스펙상으로 불필요한 추가를 하지는 말자 라고 결론내려지는 것 같다.  
  
  
<br><br><br>  

# 결론  
form method로는 get post 만 지원할거다. (제안이 들어와도 계속 리젝되는중)   
RESTful 하지 않더라도 URL에다가 DELETE PUT이 지닌 의미를 담던가,  
아니면 각종 엔진들이 formaction을 추가적으로 지원하니까 그걸 이용해라.     
(ex. 이어서 볼 thymeleaf + spring 이 지원하는 hidden http method 같은 것들)  
  
<br><br><br>
    
---  
  
# 2. 사용) Spring Thymeleaf 에서 Form Method로 Delete 사용하기

---
  
# 개요    
1. 사실 모든 추가 메서드 요청들은 처음에는 post로 들어온다    
2. 하지만 th:method="delete"로 해주면, thymeleaf가 해당 form에 hidden 으로 method 가 delete 라는 정보를 담아준다  
3. spring은 method 가 delete 라는 추가 정보가 담긴걸 보고, "아 이건 delete method"로 처리하면 되겠구나 라고 판단한다  
4. 이를 위해 spring 처리 과정 중간에 request를 가로채서 hidden delete 가 들어가 있으면 http Delete method 로 바꿔주는 필터가 들어간다     
  
  
<br><br><br>  
  
# 사실은 전부 post 요청이야  

> ➡️ 1. 사실 모든 추가 메서드 요청들은 처음에는 post로 들어온다    
> ➡️ 2. 하지만 th:method="delete"로 해주면, thymeleaf가 해당 form에 hidden 으로 method 가 delete 라는 정보를 담아준다  
> 3. spring은 method 가 delete 라는 추가 정보가 담긴걸 보고, "아 이건 delete method"로 처리하면 되겠구나 라고 판단한다  
> 4. 이를 위해 spring 처리 과정 중간에 request를 가로채서 hidden delete 가 들어가 있으면 http Delete method 로 바꿔주는 필터가 들어간다     
  
```thymeleaf
<form th:field="'postDelete'"
        th:action="|@{/board/free}|"
        th:method="delete"
        th:object="${post}">
    <input type="hidden" th:field="*{id}">
    <button class="btn btn-primary" type="submit">
      글 삭제
    </button>
</form>
```
th:method="delete" 니까 당연히 HTTP 요청도 delete로 넘어가겠지? 라고 생각할 수 있다.   
하지만 실제로는 템플릿 파싱을 거치면 아래와 같이 바뀐다  

```html
<form field="postDelete"
        action="/board/free"
        method="post">
    <input type="hidden" name="_method" value="delete"/>
    <input type="hidden" id="id" name="id" value="24">
    <button class="btn btn-primary" type="submit">
      글 삭제
    </button>
</form>
```
method="post" 로 그냥 바뀔 뿐이다  
대신 아래와 같은 히든 필드가 들어갔다  

```html
<input type="hidden" name="_method" value="delete"/>
```
이러한 input hidden 타입으로 추가 method에 대한 정보를 담은것을 Spring에서는 **Hidden Http Method** 라고 한다  
위 필드를 통해서 해당 post 요청을 delete method로 취급해달라는 의미를 담았다  
그러면 이제 이걸 누가 delete method로 바꿔줄까?  
spring이 이를 해준다  
  
<br><br><br>  

# Spring 이 form 요청의 추가 메서드들 인식하도록 하기  
  
> 1. 사실 모든 추가 메서드 요청들은 처음에는 post로 들어온다    
> 2. 하지만 th:method="delete"로 해주면, thymeleaf가 해당 form에 hidden 으로 method 가 delete 라는 정보를 담아준다  
> ➡️ 3. spring은 method 가 delete 라는 추가 정보가 담긴걸 보고, "아 이건 delete method"로 처리하면 되겠구나 라고 판단한다  
> ➡️ 4. 이를 위해 spring 처리 과정 중간에 request를 가로채서 hidden delete 가 들어가 있으면 http Delete method 로 바꿔주는 필터가 들어간다     
  
```
@Bean
public HiddenHttpMethodFilter hiddenHttpMethodFilter() {
  return new HiddenHttpMethodFilter();
}
```
  
이렇게 Bean 을 추가해주면  
HiddenHttpMethodFilter 가 작동해서 Post로 들어왔더라도 Hidden HTTP Method가 있으면 해당 추가 Method로 바꿔준다  
  
이제 개발자는 @DeleteMapping 등을 이용해서  
Form에서 날아온 post지만 delete인 요청들을  
HiddenHttpMethod로 받을 수 있게된다.    
