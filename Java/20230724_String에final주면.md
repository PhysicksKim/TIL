# 배경지식 : Java의 String에 관한 기본 지식    
      
![image](https://github.com/PhysicksKim/TIL/assets/101965836/5152d1e2-1a54-42d4-883c-0ae4fa57e993)    
    
Java 에서는 String 타입을 String Pool이라는 개념을 사용해서 Immutable 로 관리한다.    
    
위 GPT 설명에도 나와있지만, 다시 설명해보면    
   
```java
String str1 = "ABC";  // 1)
Stirng str2 = "DEF";  // 2)
String str3 = "ABC";  // 3)

str1 = "DEF"; // 4)
```
  
1. str1 선언 시, 이전에 생성된 "ABC" 객체가 없다. 새로 "ABC" 객체를 생성한다.  
2. str2 선언 시, 이전에 생성된 "DEF" 객체가 없다. 새로 "ABC" 객체를 생성한다.  
3. str3 선언 시, 이전에 생성된 "ABC" 객체가 있다. 기존에 생성된 "ABC" 객체를 그대로 사용한다. (참조 주소 동일)
4. str1 = "DEF" 로 변경 시, 이전에 생성된 "DEF" 객체가 있다. 기존에 생성된 "DEF" 객체의 참조값을 사용한다. (참조 주소만 변경)
  
Java의 Immutable String Pool 에 대한 설명은 [여기](https://github.com/PhysicksKim/TIL/blob/617eed1a84ed7755dfc2a6dfd43f7e4404433e35/Java/20221003_Java%EC%97%90%EC%84%9CString%EC%9D%B4Immutable%EC%9D%B8%EC%9D%B4%EC%9C%A0.md) 를 참고.  
    
<br><br><br>

---

<br><br><br>  

# 그럼 final String 은 어떻게 될까?  
  
Java에서 final 키워드는 흔히 사용되는 const 개념이라고 보면 된다.  
다만 포인터에 대해 잘 모르고, 참조형에 대한 개념이 옅다면 헷갈릴 수 있는 부분이 있다.   
  
<br><br>    
   
## Object 에 대한 final은 "참조값 변경 금지" 이다  
  
```java
final List<Integer> list = new ArrayList<>();  
list.add(1); // 정상

list = new ArrayList<>(); // 컴파일 에러     
```

Primitive(ex. int, char) 타입은 변수가 값 자체를 담고 있지만  
Object 타입에는 변수가 참조 값을 담고 있고 객체 자체는 Heap 영역에 존재한다.  
  
따라서 final Object 는 참조 값 변경을 막는 식으로 동작한다.  
그러므로 위 코드 예시와 같이 list 객체를 사용하는 것에는 문제없지만  
<code>list = new ArrayList<>();</code> 이 부분과 같이  
list 참조 주소를 변경하는 동작에 대해서는 컴파일 에러가 발생한다.  
  
<br><br>    
   
## String 은 Object 이지만 Immutable 이다  

String은 Object다.  
  
```java  
String str = "A";  
System.out.println(str instanceof Object); // true  
```
  
그런데 String이 Object 타입이면서 동시에 앞서 설명한 대로 Immutable String Pool 방식을 사용하기 때문에    
String 값을 변경하게 되면 참조 값이 변경하는 식으로 동작한다.  
  
따라서 Final String 인 경우에는   
String 값을 아예 변경할 수 없다.  
   
<br><br><br>  
   
# 정리    
    
```java  
private void method(final String str) {
  str = "ABC"; // 컴파일 에러 발생  
}
```  
  
String은 Object 이다.  
final Object 하면 참조값을 변경할 수 없다.  
  
String은 Immutable String Pool 개념 때문에  
String 값 변경을 지시하면 내부 field에 있는 Char\[] 의 값이 바뀌는 게 아니라    
String 참조변수 주소가 바뀌는 식으로 동작한다.    
  
따라서 String 에서 final 키워드를 적용하면  
String 이 Object 이지만 문자열 값을 변경할 수 없다.  
  
