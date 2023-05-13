# main은 왜 static인가  
[Why Main Method is Static in Java?](https://www.scaler.com/topics/why-main-method-is-static-in-java/)   
  
```java
public static void main(String[] args) {
  ...
}
```

# 답  
> There is no object of the class existing when the Java runtime starts.    
> This is why the main() method must be static for the JVM to load the class into memory    
> and call the main function. If the main method is not static,    
> JVM will be unable to call it since no object of the class is present.    
  
> - 내 말투 번역   
> 자바 런타임이 딱 떴을때는 아무 객체가 없다.  
> 그러므로 JVM이 main이 있는 class를 메모리에다가 로드하고 메서드를 호출하기 위해서   
> main 메서드는 스테틱이어야 한다.  
> 메인 메서드가 스테틱이 아니라면   
> main이 있는 클래스의 객체가 없으니까  
> JVM이 main 메서드를 호출할 수 없다.  
  
  
