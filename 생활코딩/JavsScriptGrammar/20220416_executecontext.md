# Execute Context
자바스크립트 코드가 실행되는 원리인 execute context에 대한 수업.  
[강의로 이동](https://www.youtube.com/watch?v=QtOF0uMBy7k)

# 강의에 사용되는 예제 코드
[링크](http://bit.ly/js-execute-context)  
  

# 강의 디버거 기본 사용
(1) 예제 코드를 html 확장자에 담아서 웹브라우저에서 파일 오픈  
(2) 검사창에서 소스에 가면 예제 코드가 나옴  
(3) 코드 줄 클릭하면 중단점이 설정이 됨  
(4)  
![image](https://user-images.githubusercontent.com/101965836/163657576-b13d69c3-cdd7-426f-9b2e-df399a19fe30.png)  
여기서 중단점 이후 어떻게 실행할지 선택가능  
(5) 선언된 함수나 변수 등은 scope 에서 확인 가능하다  


# 전역 변수
```
n0='n0'
```
예제1의 2번째 줄에서 위와 같이 선언하였다.  
이렇게 앞에 아무것도 써넣지 않으면 global에서 저장된다  

# 변수 호출시 찾는 순서
자바스크립트는 변수를 읽으려 할 때 scope에 나오는 순서에 따라 찾는다  
먼저 local에 n0가 있나? 없다. 다음.  
script안에 n0가 있느냐? 없다. 다음.      
그러면 global 안에서 n0를 찾는다? 있다.   
global n0를 반환한다.   


# 그러면 언제 어디에 저장 되는거지?
예시를 보자  
```
n0='n0';  
var v0 = 'v0';
```
앞에 아무것도 붙이지 않거나, var를 붙인 경우 global에 저장된다  
  
```
let l0 = 'l0';
const c0 = 'c0';
```
앞에 let과 const를 붙이면 local에 변수가 저장된다  
  
근데 함수 안에서 var로 선언한 경우 local로 들어간다

# 정리
![image](https://user-images.githubusercontent.com/101965836/163658959-8a1a3624-73c6-4b4b-b2c6-f932a42d7cc7.png)
