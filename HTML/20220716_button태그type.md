# button 태그의 type  
button 태그의 type 속성에는 주로 submit , button 2가지가 쓰이고  
이외에는 reset 이라는 속성도 있다.  
reset은 그냥 폼 안에 모든 input 의 value를 초기화시켜주는 기능이며, 지금 이 글에서는 따로 논하지 않는다.  
  
# submit 과 button 
type="submit"은 폼에 있는 input 데이터들을 지정된 url과 method로 전송하도록 동작한다.  
하지만 type="button"은 아무 동작도 하지 않는 버튼으로 만든다.  
  
아무 동작도 하지 않는다는 것은,  
완전히 form태그와 무관한 버튼이 된다는 것이다.  
   
# button 속성을 쓰는 이유  
form 태그 안에서 form 데이터를 전송하는 기능만 쓰지는 않을 것이다.  
예를 들어 피자를 인터넷으로 주문할 때  
주소 입력과 관련된 또 다른 외부 프로그램을 활용하기도 하며  
결제할때 이니시스 같은 결제 서비스를 활용하기도 한다.  
이럴 때 버튼은 form 데이터를 전송하는 식으로 동작하는 게 아니라  
자바 스크립트로 어떤 동작을 하도록 만들어 줘야 할 것이다.  
이럴때 button 속성으로 form과 관련된 동작을 하지 않도록 하고  
onclick="..." 으로 자바스크립트 명령을 넣어줘야 한다.  

# 예시
```html
<form ... >
  
  ...
  
  <button type="submit" class="btn btn-primary" 
          formaction="/login" formmethod="post">
              Login
  </button>
  <button type="button" class="btn btn-secondary"
          onclick="location.href='signUp.html'"
          th:onclick="|location.href='@{/signUp}'|">
              Sign Up
  </button>
</form>
```
위의 Login 버튼은 form 데이터를 전송해야하니까 type="submit" 으로 했고  
아래의 Sign Up 버튼은 form 데이터와 무관하게, 그냥 /signUp 으로 이동해야 하니까  
type="button"으로 하고 onclick="..."로 동작을 정해준 것이다.  
  
<br>
   
# 근데 submit으로 하고 get 으로 보내버려도 되지않아요?  
```html
<button type="submit" class="btn btn-primary" 
        formaction="/login" formmethod="post">
            Login
</button>
<button type="submit" class="btn btn-secondary"
        formaction="/signUp" formmethod="get">
            Sign Up
</button>
```
이렇게 바꾼다면 어떻게 될까?   
  
정답은 이렇게 해도 signUp 페이지로 동일하게 이동한다.  
  
그러면 이 방식이 좋은 방식일까?  

아니다.   
왜냐하면 저렇게 해두면 signUp 페이지로 이동 할 때  
기존 로그인 페이지에서 form의 input 데이터가 쿼리파라미터식으로 전달된다.  
뭔말이냐면  
```
http://localhost:8080/signUp
```
우리는 여기로 이동하고 싶은데  
```
http://localhost:8080/signUp?userId=&userPassword=
```
이렇게 의미없이 쿼리파라미터가 전달된다.  
  
저 뒤에 붙은 쿼리 파라미터의 출처는  
이전 페이지였던 login페이지의 form 태그 안에 있던  
id / password input태그에서 온 것이다.  
  
그냥 지저분해지고 끝 아니냐? 라고 생각할 수 있는데  
만약 signUp form에서도 input태그 name에서 userId 또는 userPassword를 쓴다면?  
  
그러면 signUp 폼 데이터 전송에서 userId userPassword가 쿼리 파라미터에 있던 값까지 해서 중복으로 전달된다.  
  
뭔말이냐면  
```
http://localhost:8080/signUp?userId=loginpage&userPassword=loginpage1234
```
이렇게 넘어왔고,  
마찬가지로 signUp에서도 id password 입력 칸에다가 똑같이 name="userId" name="userPassword" 라고 썼다면  
최종적으로 signUp post 메시지에다가는 
```
userId=loginpage,signuppage , userPassword=loginpage1234,signuppage1234  ...
```
이렇게 ,로 구분되어서 값이 쿼리파라미터에 있는 값이랑 input 태그에 있는 값이랑 같이 둘 다 넘어가버리게 된다.  
  
따라서 이런 일이 생기지 않도록  
type="submit" 으로 해서 페이지 넘기지 않도록 하자  
  
  
