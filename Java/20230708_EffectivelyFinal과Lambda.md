[Oracle 공식 reference](https://docs.oracle.com/javase/specs/jls/se8/html/jls-4.html#jls-4.12.4)  

# Effectively Final : 사실상 final  
final을 붙이지는 않았지만, 코드상에서는 변경되지 않아서 사실상 final인 경우를 말함.  
  
<br>  
  
# Effectively Final 개념 등장 이유 : lambda에서 에러 생길 수 있음  
  
- 정상 동작 (effectively final)  
   
```java  
int num = 10;
  
Runnable runnable = () -> System.out.println("number : " + num);
  
runnable.run();
```  
  
```
number : 10
```
   
위와 같이 정상적으로 실행된다.   
   
<br><br>    
  
- 중간에 **지역 변수 바뀌는** 경우  
  
```java  
int num = 10;
  
Runnable runnable = () -> System.out.println("number : " + num);

num = 20; // 여기서 문제 발생  

runnable.run();
```  
  
```  
java: local variables referenced from a lambda expression must be final or effectively final  
```  
   
지역 변수가 final 이거나 effectively final 이어야 한다는 에러다.   
   
<br><br>  
   
# 이거 왜 Effectively Final 에러가 나지?  

```java
public int[] solution(String[] genres, int[] plays) {
  Map<String, Integer> map = new HashMap<>();
  
  for(int i = 0 ; i < genres.length ; i++) {
    map.compute(genres[i], (k,v) -> (v==null) ? plays[i] : (map.get(genres[i])+plays[i]));
  }

  ...
}
```
  
여기서 왜 compute() 의 람다식 부분에서 effectively final 에러가 나지?  

## 배열에 있는 값을 그대로 쓰면 에러가 나더라  

### 1. 에러 나는 경우
   
```java
for(int i = 0 ; i < genres.length ; i++) {
  map.compute(tempGenre, (k,v) -> 
    (v==null) ? plays[i] : (map.get(genres[i]) + plays[i])
  );
}
```

<br><br>  
  
### 2. 에러 해결

```java
for(int i = 0 ; i < genres.length ; i++) {
  String tempGenre = genres[i];
  int tempPlay = plays[i];

  map.compute(tempGenre, (k,v) -> 
    (v==null) ? tempPlay : (map.get(tempGenre) + tempPlay)
  );
}
```

<br><br>  
  
위 두 방식 차이점은   

- lambda 식에다가 배열 참조로 전달
- 배열에 있는 값을 꺼내서 값으로 전달
  
여기서 핵심은  
참조로 전달했기 때문에 값이 달라질 수 있다는 점이다.  
  
<br> 
