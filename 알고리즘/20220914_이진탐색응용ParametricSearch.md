[이전에 풀었던 문제 - 백준 1654 랜선자르기](https://github.com/PhysicksKim/TIL/blob/main/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98/%EB%B0%B1%EC%A4%80/1654_%EB%9E%9C%EC%84%A0%EC%9E%90%EB%A5%B4%EA%B8%B0_%EC%9D%B4%EB%B6%84%ED%83%90%EC%83%89%EC%9D%91%EC%9A%A9parametric%20search.md)  
  
# 구현 코드  
```
private static int parametricBinarySearchRecur(int[] arr, int target, int start, int end){
    if(start == end) {
        return start; // 인덱스가 딱 하나로 수렴했으면 해당 인덱스 반환  
    }
    
    if(start>end){
        return -1; // 적절한 값을 찾지 못했을 경우
    }

    int middle = (start+end)/2;
    
    if (target >= arr[middle] ) {
        return parametricBinarySearchRecur(arr, target, start, middle);
    } else if (arr[middle] > target) {
        return parametricBinarySearchRecur(arr, target, middle+1, end);
    } 
    
    // else if (arr[middle] == target) {
    //    return parametricBinarySearchRecur(arr, target, start, middle);
    // }

    return -2; // -2가 나오면 구현상 에러
}
```

  
# Parametric Search
이전에 문제로 처음 접했던 Parametric Search에 대해서 한번 보자.  
  
<br><br>
> ### 필요 선행 지식  
> 1. 이분탐색
> 2. Lower Bound, Upper Bound [참고](https://st-lab.tistory.com/267)  
> 3. 한번쯤은 Parametric Search를 구현해봤어야함.  
> (이 글은 Parametric Search의 구현에서 까다로운 부분을 설명하기 위해 쓴 글입니다. 기본 알고리즘 원리는 설명하지 않습니다)     

<br><br>  
  
# 문제는 각종 경계조건들  
  
### 경계조건이란?   
간략히 말하자면   
알고리즘의 핵심이 되는 논리 말고   
구현 과정에서 고려되어야 하는 세부내용들을 말한다.   
   
경계 조건(Boundary Condition) 이라는 말 그대로  
   
- Binary Search가 어디로 수렴해야하는가?   
  
같은 구현시 고려되어야 하는 부분들을 경계조건이라 한다.  
(엄밀한 용어인지는 사실 잘 모르겠다. [이 강의](https://www.boostcourse.org/cs204/joinLectures/145114)에서 그런 늬앙스로 말하길레 그냥 차용해봤다)  
  
  <br>  
  
## 문제가 되는 경계조건 부분    
Parametric Search 구현 과정에서 문제가 발생하는 부분은 주로 아래와 같다  
  
1. 재귀 호출 할 때 인덱스 범위를 어떻게 설정해야하지?   
2. 등호를 어떻게 설정해야하지?  
  
이에 대해 차근히 알아가보자  
  
<br>  
  

   
# 1. 재귀 호출 인덱스 범위 설정  
재귀 호출 인덱스 범위란  
탐색 범위를 절반으로 줄여갈 때  
start, middle, end를 가지고 어떻게 줄여야 하는지를 말한다  
  
예시로 보자  

### 예시 1 - 왼쪽으로 줄이기  

|start| ... | ... | target | middle | ... | ... | ... | ... | end |
|---|---|---|---|---|---|---|---|---|---|
|10|9|8|7|6|5|4|3|2|1|

이렇게 있으면 어떻게 인덱스를 줄여야 할까?  

start ~ middle 까지로 인덱스를 줄여야 할까?  
아니면 start ~ middle-1 까지 인덱스를 줄여야 할까?  
  
### 예시 2 - 오른쪽으로 줄이기  

|start| ... | ... | ... | middle | target | ... | ... | ... | end |
|---|---|---|---|---|---|---|---|---|---|
|10|9|8|7|6|5|4|3|2|1|

이렇게 있으면 어떻게 인덱스를 줄여야 할까?  
  
middle ~ end 로 인덱스를 줄여야 할까?  
아니면 middle+1 ~ end 로 인덱스를 줄여야 할까?  
  
### 예시 3 - middle == target

|start| ... | ... | ... | middle == target | ... | ... | ... | ... | end |
|---|---|---|---|---|---|---|---|---|---|
|10|9|8|7|6|5|4|3|2|1|

이렇게 있으면  
start ~ middle / middle+1 ~ end 로 끊어야 할까?  
start ~ middle-1 / middle ~ end 로 끊어야 할까?  

  
