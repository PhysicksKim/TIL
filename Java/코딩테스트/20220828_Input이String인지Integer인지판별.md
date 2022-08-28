백준만 표준 입출력을 받고, 다른 코딩테스트에서는 표준 입출력을 받지 않는다.   
따라서 실제로 쓸 일이 백준 말고는 있을지 모르겠지만  
아이디어가 흥미로워서 기록해둔다.   
  
  ---
  
# String / Integer 판별

---

## 내가 떠올린 방법  
### 1. Integer.parseInt( )를 try catch로 분기 만들기  
```java
inputString = br.readLine();
try {
  // String -> Integer 변환 시도
  tempInt = Integer.parseInt(inputString);
  
  // 입력값이 Integer 일 경우 로직
  ...
  
} catch (Exception e) {
  // 입력값이 String 일 경우 로직
  ...
}
```

---

## 다른 방법  
### 2. 첫 문자가 숫자인지 체크  
이때 규칙은 아래와 같다  
1. 숫자일 경우 unsigned로 입력값이 들어온다  
2. 문자일 경우 첫 글자가 숫자(0~9)는 아니다    
  
[출처](https://www.acmicpc.net/source/10273532)  

```java
String inputString = br.readLine();
char firstCharacter = call.charAt(0);

if(firstCharacter >= '0' && firstCharacter <= '9') {
  // 숫자일 때 로직 
  
}
else {	
  // 문자일 때 로직  
  
}
```

 
