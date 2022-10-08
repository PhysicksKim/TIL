# Arrays.binarySearch() 메서드를 통한 이진탐색
Arrays.binarySearch() 메서드를 이용하면  

파라미터로  array , key 를 넘기면 해당하는 index를 return 해준다.  
  
<br><br>  
  
# ❗ 하지만 binarySearch() 메서드에는 문제가 있다  

<br>

### 1. 값 중복이 있는 경우 문제 발생 - Bound 문제  
```java

int[] duplArr = {0,2,2,2,2,3,4,5};
int findIdx = Arrays.binarySearch(duplArr, 2);
System.out.println(findIdx);

// 출력 결과
// 3
```
   
이진 탐색 도중 일치하는 값을 찾으면 곧바로 return 한다  
위 예시의 경우,  
첫 시행에서 length = 7 이니 mid = 3 이 되고, arr\[mid\] = 2 라서  
곧바로 mid = 3 을 retrun 해버린다.  
  
따라서 값의 중복이 있을 경우   
Upper Bound 또는 Lower Bound 같은 일관성 없이 인덱스를 return 한다   
그러므로 코딩테스트의 이진탐색 응용 문제에는 사용하기 힘들다   
  
<br><br><br>  
  
### 2. 정확히 값이 주어져야만 한다 - Parametric Search에 사용하기 곤란  
  
<br>  
  
예를 들어 20세 이상인 사람만 얻기 위해서  
아래 배열에서   
**처음으로 20 이상 값이 등장하는 인덱스**를 얻는다고 해보자.    
  
15 15 17 18 18 19 19 21 21 21 23 24  
  
이렇게 되어있다면   
arr\[idx\] >= 20 이라고 탐색해서 인덱스를 얻어야 한다  
근데 배열에 20 값이 없고 21만 있으므로  
정확히 딱 20 값이 없기 때문에  
Arrays.binarySearch(arr, 20) 으로 해버리면  
값을 찾을 수 없다고 나온다.  
  
<br>  
  
따라서 Parametric Search 문제에서도 Arrays.binarySearch() 메서드를 쓸 수 없다.  
  

