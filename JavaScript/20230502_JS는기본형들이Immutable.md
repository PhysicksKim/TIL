# JavaScript 에서는 기본형(Primitive Type) 값은 Immutable 이다  

[원시 값 - MDN Web Docs](https://developer.mozilla.org/ko/docs/Glossary/Primitive)  
  
JS에서는  
Number Boolean String 같은 기본 값들을  
Immutable로 관리한다  
  
> 원시 값에는 7종류, string, number (en-US), bigint (en-US), boolean, undefined, symbol, 그리고 null이 존재합니다.
  
## 설명 
  
변수는 <code>let a = 1;</code> 이렇게 했을 때   
메모리 상에서는  
  
|변수|주소|값|
|---:|:---:|:---|
|a|0x0010|1|
||0x0020||
||0x0030||
  
이렇게 저장되어 있다  
  
그럼 <code>a = 3;</code> 이렇게 변경하면 어떻게될까?  
  
### 오해    
  
|변수|주소|값|
|---:|:---:|:---|
|a|0x0010|3|
||0x0020||
||0x0030||
  
대부분 경우 이렇게 예상하겠지만  
JS에서는 이런 식으로 값이 교체되지 않는다  
  
### 실제 동작  
 
|변수|주소|값|
|---:|:---:|:---|
||0x0010|1|
||0x0020||
|a|0x0030|3|

완전히 새로운 메모리 공간에   
값을 새로 할당하고 변수는 주소를 옮긴다  
  
<br><br>  

## 무엇이 Immutable 인가? - 값이 Immutable  
  
변수가 고정되어있고 값이 바뀔건가?  
아니면  
값이 고정되어있고 변수의 위치가 바뀔것인가?  
  
Javascript 에서는 기본형 변수들은  
값이 메모리 주소상에 고정되고  
변수가 가리키는 메모리 주소가 바뀐다.  
  
<br><br>  

## 왜 Immutable 사용? - 성능, 안정적으로 동작  

[Why is Immutability so important in Javascript](https://www.geeksforgeeks.org/why-is-immutability-so-important-in-javascript/)  
[변하지 않는 상태를 유지하는 방법, 불변성(Immutable)](https://evan-moon.github.io/2020/01/05/what-is-immutable/#%EB%B6%88%EB%B3%80%EC%84%B1%EC%9D%84-%EC%A7%80%ED%82%A4%EB%A9%B4-%EC%96%B4%EB%96%A4-%EC%A0%90%EC%9D%B4-%EC%A2%8B%EC%9D%80%EA%B0%80%EC%9A%94)  
  
1. 성능  
캘린더 프로그램을 생각해보자.  
"2023" 라는 숫자가 얼마나 많이 쓰일까?  
1년을 다 담고 있다면 365개나 쓰일텐데  
불필요하게 365개의 변수가 전부 제각각 2023 이라는 값을 따로 가지고 있다면  
메모리 낭비가 심할거다  
반면, 하나의 공간에다가 2023 이라는 값을 딱 저장해두고  
365개의 변수가 모두 2023이라는 값이 담긴 곳을 가리키고 있다면  
앞선 예시와 비교해서 훨씬 더 메모리가 절약될 것이다.  
  
2. 안정적으로 동작(Predictability) 
전역 변수를 생각해보자.  
전역 변수의 문제는 어디서든 변수에 접근할 수 있으니  
누가 또 변수의 값을 바꾸면 도대체 어디서 문제가 발생했는지 찾기 힘들다  
이 문제를 전역변수 관점보다 더 파고들어서   
메모리 레벨에서 변수와 값의 불변성에 대해 생각해보자.  
메모리 레벨에서 값이 다른 곳에서 바뀔 수 있으면 또 전역변수때와 같은 문제가 발생한다.  
따라서 안정적으로 예측 가능하게 동작하려면 불변성을 지켜야 한다  




