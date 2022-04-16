[강의로이동](https://www.youtube.com/watch?v=iPe0noahrBY)  
  
> 자바스크립트에서 콜백이란 무엇이고, 콜백을 어떻게 사용하는지 알아봅니다.
  
# First Class Citizen    
(1) 변수에 할당할 수 있는가?   
(2) 리턴 값으로 줄 수 있는가?  
두 조건을 만족하면 일급시민이 될 수 있다!  

# 일급 시민 이야기가 왜 갑자기 나오는가?
지금까지 봐온 콜백 함수를 생각해보자. 함수 안에 인자로 함수를 전달한다. 이는 곧 함수가 마치 변수처럼 전달되는 모습이라 할 수 있다.  
그러면 C에서는 함수를 인자로 전달할 수 없고(어쩌면 있을지도), 강의에서도 말하듯 java에서는 변수에 함수를 할당할 수 없다.   
자바스크립트에서는 변수에 함수를 할당할 수 있으므로, Callback이란게 가능해진다. 차근히 살펴보자.  

# 일급시민 조건 (1) - 변수 할당 가능?
## 1은 변수에 할당 되는가?
#### 가능하다
```js
val firstClass = 1;
```

## if문은 변수에 할당 되는가?
#### 불가능하다
```js
// val noFirstClass = if( ... ) {
          ... 
        }
```

## 함수는 변수에 할당 되는가?
#### 가능하다
```js
val firstClass = function ( ... ) {
  ...
  }
```

# 일급시민 조건 (2) - 리턴 값으로 가능?
## 1은 return 할 수 있는가?
#### 말할 필요 없이 당연하다

## if문은 return 할 수 있는가?
#### 당연히 안된다

## 그러면 함수는 return 할 수 있는가?
```js
function fn(){
  val = function(...){
          return ...;
      }
  return val;
}
```
#### 함수도 return 할 수 있다.

# 일급 시민 이야기 정리
따라서 JavaScript에서는 함수도 리턴할 수 있고, 함수를 던지고 받고 주고 지지고 볶고 할 수 있다.  
이와 더불어 callback이라는 개념으로, 함수를 건내주고 배열의 각 요소마다 이 함수를 실행해라는 등의 동작도 가능해지게 된다.  
즉 callback이 등장할 수 있는 이유도 이러한 일급 시민 맥락에서 함수를 변수에 할당할 수 있다는 것 덕분이다.


# 직접 콜백 함수 활용하는 함수 선언해보기

```js
words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present']

function myFilter(origin, callback){
  let result = [];
  for(let i=0; i<original.length; i++){
    let current = origin[i];
    if(callback(current)){
      result.push(current);
    }
  }
  return result;
}

filteredWords = myFilter(words, element=>element.length>6);
console.log(filteredWords); // ['exuberant', 'destruction', 'present']
```
