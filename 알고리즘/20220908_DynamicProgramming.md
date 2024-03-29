# 1. Dynamic Programming (동적 계획법) 이란?  

### 정의는 나중에  
쉽게 말해서 **"앞서 구한 답 재활용하기"** 이다  
  
그러므로 **Dynamic Programming 알고리즘**은 
이진 탐색, 길찾기 알고리즘 같은 구체적인 문제 풀이 도구같은 알고리즘이 아니다.    
그냥 문제를 **푸는 방향성을** **제시**해주는 것이다.  
  
  
## 예시  
  
**피보나치**가 가장 대표적인데  
만약 재귀로 구현하면  
```
f(n)
  if n = 1 or n = 2,
    return 1
  else
    return f(n-1) + f(n-2)
```
이렇게 된다  
  
### 재귀로 했을 때 문제점   
만약 f(5) 호출한다면  
f(1) f(2)를 만날 때 까지  
계속 재귀호출이 이뤄진다.  
  
  
![image](https://user-images.githubusercontent.com/101965836/189092281-26a21ce5-9d62-49f0-bbe7-ce677d4bc3f5.png)  
[출처](https://hongjw1938.tistory.com/47)  
위 그림처럼 f들이 많이 호출되는데  
잘 보면 f(2) f(3)이 2번 이상 호출되므로   
값을 따로 저장해둔다면 재활용 할 수 있다는 것을 알 수 있다.  
  
만약 f(100) 같이 엄청 큰 수가 된다면?   
이럴때는 f(10) f(20) 같은 값들이 무수히 많이 호출될꺼고   
또 매번 f(10) f(20)을 호출하고자 또 무수히 많은 피보나치 함수들을 재귀호출하게 될 것이다   
   
   
<br>  

### 값을 재활용하는 Dynamic Programming  

따라서 앞서 본 피보나치 재귀의 문제점을  
Dynamic Programming을 도입해서 해결한다.  
  
DP가 뭐라고 했던가?  
**"값을 재활용"** 하는 것일 뿐이라고 했다.  
  
그래서 그냥 피보나치 저장해두는 배열 fiboArr을 만든 다음  
  
1. 만약 fiboArr == 0 (생성시 초기화해둔 값) 일 경우  
한번도 호출된적이 없으므로  
재귀를 통해서 값을 구해나가고  
  
2. 만약 fiboArr != 0 일 경우  
한번이라도 호출된적이 있어서 값을 저장해둔 것이므로  
값을 그냥 갖고와서 쓰면 된다  
  
<br><br><br>  
  
# 2. 언제 Dynamic Programming을 쓸 수 있는가?  
  
흔히 정의하기를  
"큰 문제를 작은 문제로 나누어서 해결할 때"  
라고 말 한다  
  
  <br>  
  
여기서 의문이 들 수 있다
### 재활용이랑 작은 문제로 나누는거랑 무슨상관이냐?  
이에 대해 생각해보자  
  
<br><br>  

## 1) 반복적으로 어떤 값이 필요한 경우  
앞서 본 피보나치가 이 경우이다  
만약 f(10)을 구하려면  
f(8)은 2번 f(7)은 3번 ... f(3)은 21번 f(2)는 34번이나 호출된다  
같은 값을 구하려고 이렇게 같은 재귀를 반복할 필요 없이  
그냥 배열에다 저장해두고 재활용하면 된다  
  
이를 다시 앞서 본 "큰 문제를 작은 문제로 나눠서 해결하기" 관점으로 해석해보면  
f(10)이라는 큰 문제를 
f(9) f(8) ... f(3) f(2) 라는 작은 문제로 나누고,  
각 문제의 정답을 저장했다가 썼기에  
다이나믹 프로그래밍이라고 보면 되는 것이다.  

<br><br>  

## 2) 최적화된 경로를 구할 때  
무슨 말인가 하면  
![image](https://user-images.githubusercontent.com/101965836/189098574-cff8bb1a-3594-411d-8c17-d5a93e4f8046.png)  
이런 최단경로 문제에서도  
A->X 의 최적 경로와, X->B 최적 경로 문제로   
큰 문제를 작은 문제로 나눌 수 있다.  
따라서 A->X 최적 값을 구한다음 저장해두고,  
X->B 최적 값을 구해주면 된다.  
  



