[참고](https://hianna.tistory.com/546)  
  
# try - with - resoureces : 자동으로 자원을 반환  
try 구문이 끝나면 자동으로 특정 자원들을 반환한다. 
  
<br><br><br>

# try - catch - finally 

기존에는 finally 구문은    
try로 끝나든, 예외 발생해서 catch로 끝나든   
항상 마지막에 실행되는 finally 부분을 통해서  
자원을 반환했다.  

```java
try {
  scanner = new Scanner(new File("input.txt")); // 자원 생성
  System.out.println(scanner.nextLine());
} catch (FileNotFoundException e) {
  e.printStackTrace();
} finally {
  if (scanner != null) {
      scanner.close(); // 자원 반납
  }
}
```

<br><br><br>  

# try - with - resoureces
  
> try (자원 선언) {}     
  
자원 선언을 try의 () 안에 해주면   
try catch 가 끝난 후 자원을 자동으로 반환한다.  
  
```java
try (Scanner scanner = new Scanner(new File("input.txt"))) {
  System.out.println(scanner.nextLine());
} catch (FileNotFoundException e) {
  e.printStackTrace();
}
```
  
<br>  
  
### 자동 반환 원리 - AutoCloseable 인터페이스  
[AutoCloseable 인터페이스 specification](https://docs.oracle.com/javase/8/docs/api/java/lang/AutoCloseable.html)  
  
> try (자원 선언) {}     
  
자원 선언 부분에서 객체를 생성할려면  
AutoCloseable 인터페이스를 구현해줘야 한다   
  
AutoCloseable 인터페이스에는 close() 메서드가 있는데  
close() 메서드가 자원 반납 과정을 수행한다  
  
try catch 가 끝나면 resources 의 close()를 실행해서 자원을 반납하게 되는 방식이다.  
  

