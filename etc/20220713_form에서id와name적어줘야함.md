# 계기  
```html
<form class="px-3 py-4" th:action method="post">
  <h2>회원 가입</h2>
  <div ... >
     ...  
  </div>
  <button type="submit" class="btn btn-secondary">회원 등록</button>
</form>
  
```
이렇게 해놨는데  
spring controller로 폼데이터가 안넘어옴  
뭔 문젠가 싶어서 보는데  
크롬 개발자 도구로 봐도 body에 메세지가 안담겨있고  
@RequestBody 같은걸로 봐도 메세지가 안넘어옴  
  
  <br><br><br>
  
# 해결 방법 
form 태그에다가 id랑 name을 지정해줘서 해결했다.  
왜 그런지는 잘 모르겠는데  
일단 id name 둘 다 넣어야지만 폼데이터가 넘어왔다.  
  
추측해보면 아마 어떤 폼인지 명확하게 구분해줄 name이 필요해서 그런 것 같은데   
id까지 필요한 이유는 잘 모르겠다  

```
<form name="signupForm" id="signupForm" 
      class="px-3 py-4" th:action method="post">
  <h2>회원 가입</h2>
```

---

# 추가 ; 각 input 태그에다가 name 속성 적어줘야 함
input 태그에 있는 name 값을 바탕으로  
스프링에서 ModelAttribute가 동작하기 때문이다.  
그러므로 바인딩 하고자 하는 객체에 맞춰서 name 값을 input 태그에 각각 적어줘야 한다.  
