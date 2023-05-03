#### 아무말     
  
Javascript는 이상하다.  
  
어떻게 동작하는지 이해하려면  
이전에 배웠던 C C# Python Java 보다 더 많은 학습량을 요구한다  
  
그래서 싫긴한데  
알고보면 또 완전 난장판은 아니고  
어느 정도는 이유가 있는 것 같다.  
  
그 중에 참 기이했던게 함수의 호이스팅이다.  
JS는 스크립트 언어인데 호이스팅이 된다는 게 참 기이하다.  
  
<br><br>
  
---
  
<br><br>  
  
# 함수 정의 기본  

```javascript  
function add(x, y) {
  return x + y;
}
add(2, 5);
```

<br> 

# 함수 리터럴  
- 리터럴이란?  
값 그 자체. <code>1</code> <code>true</code> 같이 값 그 자체를 말한다.  
  
- 상수와 차이  
상수는 변수에다가 값을 집어넣고 이건 변하지 않는거야 라고 정해둔거다.  
반면 리터럴은 값 그 자체를 말한다.   
<code>var a=1;</code>이라고 할 때, 1이라는 값이 어디에 집어넣어져 있는게 아니다.     
그냥 1이라는 값은 1이니까.  

- 왜 리터럴이라는 용어가 있냐?  
변수와 구분하기 위함인 것 같다  
  
### 함수 리터럴은?  
Javascript 에서 함수는 객체 타입의 값이다.  
따라서 숫자 값을 리터럴로 생성하는 것처럼, <code>var a=1;</code>    
객체를 객체 리터럴로 생성하는 것처럼, <code>var person = {name: "kim", age: 20};</code>  
함수도 함수 리터럴로 생성할 수 있다.  
  
```javascript  
var f = function add(x, y) {
  return x + y;
}
```
이렇게 하면  
마치 <code>var a=1;</code> 과 마찬가지로   
f 라는 변수 이름(지정자)을 통해서 함수에 접근할 수 있게 된다  
  
<br>  
   
# 함수 정의 방법 4가지  

### 1. 함수 선언문  
```js
function add(x, y) {
  return x + y;
}
```
이름 생략 불가  
함수 선언문은 **표현식이 아닌 선언문**이다  
> 표현식이 아닌 문은 변수에 할당할 수 없다  
> 따라서 위 방식대로 <code> var add = function add(x, y) { ... };</code> 할 수 없다  
  
### 2. 함수 표현식  
```js
var add = function(x, y) {
  return x + y;
};
```
일급 객체임을 활용해 변수에 할당할 수 있다  
> 일급 객체 : 값의 성질을 갖는 객체  
  
### 3. Function 생성자 함수  
```js
var add = new Function('x', 'y', 'return x + y');
```
일반적이지 않고, 바람직하지 않다.  
클로저를 생성하지 않으며, 함수 선언문이나 표현식 방식과 다르게 동작한다.  
  
### 4. 화살표 함수(ES6)  
```js
var add = (x, y) => x + y;
```
화살표함수의 특징은  
this 방식이 다르고, prototype 프로퍼티가 없고 arguments 객체를 생성하지 않는다.  
모르면 넘어가자. 나중에 나올 문법이다   
  
<br>  
  
# 호이스팅(hoisting)  
JS에는 특이한 동작이 있다. 바로 호이스팅 이라는 건데  
함수 선언이 호출보다 뒤에 나와도 정상적으로 동작한다.    
  
다르게 말하면  
함수 선언문이 코드의 선두로 끌어 올려진 것처럼 동작한다.  
  
```javascript
console.dir(add);

console.log(add(2, 5));

function add(x, y) {
  return x + y;
}
```
대부분 언어에서는 선언이 먼저 이뤄져야 한다.  
하지만 JS에서는 조금 예외적으로   
위와 같이 **함수 선언문**을 사용하는 경우에는 **정상적으로 동작**한다    
   
반면 **함수 표현식**을 사용하는 경우에는 **호이스팅이 이뤄지지 않는다**    
```javascript
console.dir(sum); // undefined

console.log(sum(2, 5)); // TypeError: sum is not a function

var sum = function (x, y) {
  return x + y;
}
```
  
<br>
  
# 즉시 실행 함수  
함수 정의와 동시에 즉시 호출되는 함수를 말한다  
```javascript
(function () {
  var a = 3;
  var b = 5;
  return a * b;
}());
```
  
{} 다음에 () 가 오는 것이 좀 이상하다 생각할 수 있는데  
  
```java
var multiple = function () {
  var a = 3;
  var b = 5;
  return a * b;
};
multiple();
```
위 코드를 토대로 다시 보면 <code>함수면();</code> 이렇게 함수를 실행하니까  
<code>(함수{}());</code> 이렇게 된다고 보면 된다.  
  
### 주의할 점 
1. 함수 코드 블록에서 닫는 중괄호(}) 다음에는 암묵적으로 세미콜론(;)이 추가된다  
```js
function foo() {}(); // => function foo() {};();  
```
위와 같은 경우, 결과적으로 ();만 단독으로 나오게 되어서 다음과 같은 에러가 발생한다  
  
```
caught SyntaxError: Unexpected token ')' 
```
  
2. 먼저 함수 리터럴을 평가해서 함수 객체를 생성하는게 중요하다  
설명하면  
함수 생성이 먼저 되도록 <code>(function () {...})();</code> 이렇게 ()로 감싸라는 뜻이다  
다만 예외적으로 <code>(function () {...}());</code> 이렇게도 동작하도록 되어있다.  
이 부분은 별도로 암기방식을 찾기 보다는   
어차피 자주 나오는 부분이니 익숙해지도록 하자.  
  
<br>  
  
# 콜백 함수 & 고차 함수  
```java
// 고차함수
function repeat(n, f) {
  for (var i = 0; i < n; i++) {
    f(i);
  }
}

// 콜백함수 
var logging = function (i) {
  console.log(i);
}

// 고차 함수에다가 콜백 함수를 넘김  
repeat(5, logging);
```

- **콜백 함수** : 매개변수를 통해 전달되는 함수  
- **고차 함수** : 콜백 함수를 받은 함수  
  
> 고차 함수라는 뜻이 모호할 수 있는데, 수학에서 n차 방정식을 생각해보면 된다.    
> 2차 방정식이라 하면 x^2 이 들어간 식을 말하는데  
> x가 2번 곱해졌다는거다  
> 마찬가지로 고차 방정식이면 x^n 꼴로 n번 곱해진거다.  
> JS에서 고차 함수는 마찬가지로  
> 함수(함수()) 이런 꼴이니까  
> 함수가 여러번 곱해진거라서 고차함수라고 부른다  
> 라고 생각하면 된다  
  
<br>  

# 순수 함수 vs 비순수 함수
  
- 순수 함수 : arguments만 사용하며, 같은 인수가 전달되면 같은 값을 반환하는 함수  
- 비순수 함수 : arguments외에 다른 외부 상태에 의존함. 따라서 같은 인수가 전달되어도 다른 값을 반환할 수 있음  
   


