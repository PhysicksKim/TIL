# Stream 의 Collect 가 신기하게 생겼다  

### 정의
```java
<R> R collect(Supplier<R> supplier,
            BiConsumer<R, ? super T> accumulator,
            BiConsumer<R, R> combiner);
```

### 사용 예시
```java
return streamerRepository.findAll().stream().collect(
                HashMap::new,
                ((hashMap, streamer) -> hashMap.put(streamer.getName(), streamer.getHash())),
                HashMap::putAll
        );
```

collect() 는 stream 으로 순회중인 각 element 들을 Collection 에 넣어주는 매서드이다.  
그런데 생각해보면 1개나 2개는 짐작이 가지만, 왜 3개일까?  
  
처음 내 추측은 이러했다.    

<br><br><br>  

# 매개변수 1개, 2개 일 때 추측 

추측은 이해에 중요하다.  
예를 들어 <code>add(int, int)</code> 이와 같은 메서드가 있으면  
쉽게 "두 int 를 더하겠구나" 라고 이해할 수 있다.  
  
마찬가지로 Stream 에서도 매개변수의 타입과 개수로 추측해볼 수 있겠는데  
1개와 2개는 어느정도 예상이 된다. 
  
## 매개변수가 2개 라면 아마도...  
2개 부터 추측 해보는 게 적절하다.  
> 사실 매개변수 2개를 받는 collect 는 없지만, 이해를 위해서 "2개짜리가 있다면?" 부터 접근해보자.  
  
collect 는 "여기다가, 이렇게 넣어" 라고 Collection 을 만들어 주는 스트림 메서드다.  
그러면 1) 여기다가 2) 이렇게 넣어 를 지정해야 하니까 2개가 자연스러워 보인다.  
  
## 매개변수 1개는 아마도... 
앞서 말한 2개짜리를 한방에 해결하는 람다를 던져주는 것 아닐까? 간단하다.  
  
<br><br><br>  

# 매개변수 3개는 왜?  
  
그럼 2개에서 기능이 하나 더해 지는 경우가 3개인 것일텐데,  
얼핏 생각으로는 대체 뭐가 더 필요한건지 이해가 안간다.  
  

<br><br><br>  

# collect 의 병렬을 이해해야 한다    
  
stream 이 병렬로 동작한다는 사실을 아는 사람도 있을거다.  
마찬가지, collect 메서드도 병렬로 동작한다.  
  
근데, Collection 에다가 요소를 넣어주는 작업을  
병렬 작업을 수행하려면 concurrent collection 을 따로 써야한다. (ex. ConcurrentHashMap)  
근데 Concurrent Collection 은 성능상 좋지 못하고, 또 매 요청마다 동시성을 고려한 container 를 만들어 줘야 하는 것도 썩 마음에 들지 않는다.  
  
그래서 Java 에서는,   
1) 동시에 여러 스레드에서 각각의 Map 을 만들고
2) 각각의 Map 에 자기가 할당 받은 부분만큼 집어넣은 다음
3) 마지막에 하나의 Map 으로 합치자
  
라는 전략을 쓰도록 collection 을 만들어 놨다.  
  
어? 이렇게 하니까 1,2,3 으로 3개가 됐네?   
  
그렇다 이게 바로 collect() 에서 매개변수가 3개인 이유다.  
  
<br>

# collect(supplier, accumulator, combiner)  

1) supplier : 각각 스레드가 쓸 map 만들고
2) accumulator : 각각 map 에다가 요소들 집어넣어주고
3) combiner : 각각 나뉜 map 들을 하나로 합친다
  
이런 뜻이었다.  
