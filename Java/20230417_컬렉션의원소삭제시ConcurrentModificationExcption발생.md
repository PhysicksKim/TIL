[알게 된 계기](https://www.youtube.com/watch?v=rjDUpxtUPAE)  
[참고1 - 왜 ConcurrentModificationException 이 발생하나요?](https://stackoverflow.com/questions/32580801/concurrentmodificationexception-when-using-stream-with-maps-key-set)  

  
# for(Object o : list) 로는 삭제 불가   
  
```java
ArrayList<String> words = new ArrayList<>(
    Arrays.asList("a","b","c","d")
);  
  
for (String word : words) {  
  if(word.equals("a")){  
    words.remove("a");  
  }  
}  
```  
  
### 예외 발생 : ConcurrentModificationException

```
Exception in thread "main" java.util.ConcurrentModificationException
	at java.base/java.util.ArrayList$Itr.checkForComodification(ArrayList.java:1043)
	at java.base/java.util.ArrayList$Itr.next(ArrayList.java:997)
	at concurrentModificationExcepTest.concurrentModificationException(concurrentModificationExcepTest.java:14)
	at concurrentModificationExcepTest.main(concurrentModificationExcepTest.java:6)
```
  
<br><br>  

# 해결 방법 - 직접 iterator 사용  
  
iterator를 직접 사용하면 elements를 지울 수 있다.  
  
```java
Iterator<String> iterator = words.iterator();
while (iterator.hasNext()) {
  String next = iterator.next();
  if(next.equals("a")) {
    iterator.remove();
  }
}
```

```
출력 결과  
words = [b, c, d]
```
  
<br><br>  
  
---

<br><br>  

# 이유는?  
  
### 선 요약  
  
for-each loop 내부에서 삭제하려면  
직접 <code>arrayList.remove(obj)</code> 를 사용해야 하는 데  
이러면 삭제 이후에 list 내부 구조에서 변화가 발생할 수 있으므로  
인덱싱과 관련해 문제가 발생한다.  

