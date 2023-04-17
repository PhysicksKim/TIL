[알게 된 계기](https://www.youtube.com/watch?v=rjDUpxtUPAE)  
[참고1 - 왜 ConcurrentModificationException 이 발생하나요?](https://stackoverflow.com/questions/32580801/concurrentmodificationexception-when-using-stream-with-maps-key-set)  
  
> ### 인상깊은 포스팅이었다 - 처음으로 소스코드를 직접 보면서 분석함  
> 생각보다 왜 ConcurrentModificationException이 발생했는지 제대로 설명한 글이 없었다  
> 그래서 어쩔 수 없이 직접 Java 소스코드를 까서 왜 이런 일이 생기는지를 알아봤다.    
> 그리고 그 과정에서  
> 직접 ArrayList, AbstractList, Iterator, ArrayList의 내부 클래스인 Itr 등의 소스코드를 분석 해 보면서  
> "아! 이렇게 Iterator가 생성되고 반환되는구나!"  
> "이렇게 기존 ArrayList 와 Iterator를 비교하는구나!"    
> 하는 것을 제대로 알게됐다.  
>   
> 덕분에 소스 코드를 보는 실력도 늘었고    
> 생각보다 소스코드 분석이 그렇게 어렵지는 않다는 것을 배울 수 있었다  
  
<br><br>  
  
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
  
<br>
 
## 설명  
[참고1 - fail-fast](https://delf-lee.github.io/post/fail-fast/)  
[참고2 - ](https://codingdog.tistory.com/entry/%EC%9E%98%EB%AA%BB-%EC%95%8C%EA%B3%A0-%EC%9E%88%EC%97%88%EB%8D%98-java-for-each-%EA%B5%AC%EB%AC%B8%EA%B3%BC-modcount-%ED%95%84%EB%93%9C)  

### 1. ConcurrentModificationException은 ArrayList가 던진다  
```java
private void checkForComodification(final int expectedModCount) {
	if (modCount != expectedModCount) {
		throw new ConcurrentModificationException();
	}
}
```
이외에도 여러 부분에서 ConcurrentModificationException이 던져지는 것을 볼 수 있지만  
하나 알아 갈 것은  
modCount != expectedModCount 일 경우 예외가 던져진다는 것이다.  
  
뭔 말인가 하면  
먼저 modCount 는 List에 있는 element의 수를 말하고  
expectedModCount는 "정상적인 흐름을 따라갔다면 modCount만큼 수정 작업이 이뤄졌다" 라는 뜻이다  
  
즉, Exception이 터질려면  
"이 Thread 에서 정상적인 로직을 따라갔다면 수정 횟수가 이 만큼이어야 하는데, 이럴리 없을 때" 터지게 된다  
  
그러면 언제 "이럴리 없어!"가 되는가?  
바로 Concurrent 문제가 발생할 때 이다.  
흔히 동시성 문제를 예상할 수 있는 Multi Thread 에서   
같은 Collection에 element를 넣고 빼고 할 때 Concurrent 문제가 발생하는 데  
이럴 때를 예상하고 ConcurrentModificationException을 만들어두고 modCount를 계속 검사하면서 불일치가 발생하면 예외를 던지는 것이다.  
  
그런데 for-each에서는 왜 modCount 불일치 문제가 생기고  
직접 iterator를 다룰 때에는 modeCount 불일치 문제가 생기지 않을까?  
  
<br>  

### 2. 핵심은, 원본 ArrayList를 바탕으로 Iterator를 새로 생성함에 있다  
  
무슨 말인가 하면  
ArrayList.iterator()의 소스 코드를 보면  
```java
public Iterator<E> iterator() {
	return new Itr();
}
```
```java
private class Itr implements Iterator<E> {
	...
}
```
이렇게 Inner class로 Itr 이란걸 따로 생성해서  
ArrayList의 복사본에 해당하는 Iterator를 별도로 만들고  
이걸로 Iterator를 돌린다는 것을 알 수 있다  
  
그리고 이 Inner class 인 Itr 안에는 또다시 checkForCommodification() 이라는 메서드를 통해서 modCount와 expectedModCount를 비교하는데  
```java
final void checkForComodification() {
	if (modCount != expectedModCount)
		throw new ConcurrentModificationException();
}
```
여기서 modCount는 ArrayList 에 있는 필드고    
expectedModCount는 Itr에 있는 값임을 명심하자      
  
아래에 적어둔 순서대로 다시 한번 더 생각해보자  
  
1. 우리의 핵심은, "Iterator를 돌다가" "ArrayList에 변경"이 있으면 안된다는 것이다!    
2. 그 핵심에는 modCount 비교가 있다. "정상흐름이면 modCount(수정 횟수)는 이 값이어야 한다" 라는 것을 계속 체크한다    
3. 그런데 iterator() 메서드로 Iterator를 가져오면, ArrayList의 Inner class로 정의된 Itr 라는 이름으로 구현한 Iterator를 가져온다  
4. 그리고 Iterator가 돌아가는 동안, 단독으로 ArrayList에만 변형이 생겨버리면 ArrayList.Itr.expectedModCount != AbstractList.modCount 로 불일치가 생긴다  
5. 따라서 ConcurrentModificationException을 throw 한다  
  
<br><br>  

### 3. modCount 만 변하는 과정을 보자  
어우 복잡해  
자, 다시 정리 해보자  
  
1. ArrayList의 부모인 AbstractList 에 int modCount 가 있다  
2. modCount는 수정된 횟수 이다  
3. ArrayList 에서 iterator() 메서드를 쓰면, Inner Class 에서 정의된 Itr implements Iterator 를 생성해서 반환한다  
4. Itr 안에는 또 다시 expectedModCount 가 있고, 생성 시점에 expectedModCount = modCount 로 초기화된다.    
  
자 여기까지 앞서서 본 내용이고  
이제 볼 내용은  

5. Iterator에서 remove() 하면, ArrayList 로 거슬러 올라가서 <code>ArrayList.this.remove(lastRet)</code> 이렇게 하고, <code>expectedModCount = modCount</code>를 한다  
6. ArrayList에서 remove() 하면, Iterator 에서 Itr.expectedModCount는 그대로지만 ArrayList.modCount만 증가한다.  
  
이렇게 된다  
  
> 직접 소스코드를 보고  싶다면    
> 먼저 ArrayList에 들어간 다음  
> iterator() 메서드를 먼저 찾아 들어가보고  
> new Itr() 이 return 됨을 보자.  
> 그 다음 Itr() 의 expectedModCount 필드를 확인해보고  
> 그 다음에는 modCount가 어딨는지 확인하자 (AbstractList에 있다)  
> 그리고 최종적으로 expectedModCount 가 바뀌는 부분과 modCount가 바뀌는 부분을 비교해보면 된다   

### modCount vs expectedModCount

- modCount : ArrayList 의 field에 존재한다    
최초로는 AbstractList에 modCount가 등장한다.  
그리고 ArrayList 가 AbstractList를 상속하고 있으니까   
ArrayList 의 필드에 modCount가 있음을 알 수 있다  
   
- expectedModCount : ArrayList 의 Inner Class 인 Itr 의 field에 존재한다      
ArrayList.iterator() 를 호출하면 new Itr() 로 Itr의 인스턴스가 생성되고  
Itr 의 field에 expectedModCount 가 생긴다    
  
<br>  
    
### ‼ (클라이막스) ArrayList 에서 remove가 일어나면 expectedModCount가 안 바뀐다    
여기가 클라이막스다  
ArrayList에서 remove가 일어나면  
ArrayList.Itr.expectedModCount 는 바뀌지 않고 (Iterator에 있는 modCount)    
ArrayList.modCount 만 바뀐다  
  
반대로 Itr 에서 remove가 있어나면  
ArrayList.remove()를 이용해서 요소를 삭제하면서   
ArrayList.modCount 가 ++ 되고  
그 다음 곧장 ArrayList.Itr.expectedModCount 를 ArrayList.modCount 랑 똑같도록  
<code>expectedModCount = modCount;</code> 를 해준다  
  
<br>
  
### 그래서, ArrayList remove 하면 Iterator랑 불일치가 발생한다  
Iterator 에서 remove 하면   
ArrayList 를 이용해서 remove하고 <code>expectedModCount = modCount;</code> 를 해줘서  
modCount 불일치가 발생하지 않는다  
  
하지만   
Iterator가 돌아가는 도중에  
ArrayList 를 통해서 remove 하면  
ArrayList 에 있는 modCount만 바뀌게 되고  
Iterator에 있는 expectedModCount는 ArrayList 의 remove 메서드로는 갱신이 안되기 때문에  
expectedModCount 랑 modCount 가 불일치해서  
ConcurrentModificationException이 발생하게 되는 것이다.  
  
<br><br>  

---

<br><br>  

# 결론 : Iterator가 돌아가는 중에는 List 에서 수정이 일어나면 안된다  
직접 iterator를 받아서  
iterator.remove() 해야한다  
  
iterator를 돌리고 있는데 (for-each loop 같은 것)  
list.remove(obj) 해버리면  
iterator 랑 list 랑 불일치가 발생해서  
ConcurrentModificationException이 뜬다.  
  
무슨 불일치가 발생하는가?  
ArrayList 예시로 보면  
내부에 수정 횟수를 체크하는 modCount 라는 게 있다  
그런데 또 ArrayList에서 iterator를 받아서 쓰면   
iterator 내부에 또 expectedModCount 라는 게 생긴다  
  
여기서 나뉘게 된다  
iterator에서 remove 하면   
list의 remove를 호출해서 삭제해서 modCount 변경이 일어나고 
곧장 iterator의 expectedModCount와 list의 modCount를 똑같도록 업데이트 해준다  
  
하지만   
list 에서 remove 하면   
list의 modCount만 업데이트되고   
iterator의 expectedModCount는 바뀌지않는다    
      
따라서 expectedModCount != modCount 가 되고  
ConcurrentModificationException이 발생하게 된다  
  
