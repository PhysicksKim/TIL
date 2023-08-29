### 참고 
[[Node.js vs Spring] Node.js vs Spring의 차이](https://well-made-codestory.tistory.com/31)   
[자바(Java)의 장단점 정리! [+ Spring vs Node.js + Express 비교]](https://jaehoney.tistory.com/167)   
[Node.js가 Spring MVC보다 성능상 유리할까?](https://siyoon210.tistory.com/164)   


---

# 백엔드 프레임 워크들  
백엔드 프레임워크에는  
Java의 Spring  
JavaScript의 Express(Node.js)  
Python의 Django  
등이 있다.  
  
근데 왜 이렇게 다양한 프레임워크들이 있을까?  
당연히 각 언어 또는 프레임워크별 장단점이 있기 때문이다.  
  
그 중에서 가장 자주 보이는 두 백엔드 프레임워크  
Spring 과 Express(Node.js) 에 대해 알아보자.  
  
  ---
  
# 바로 결론
### Spring 장점 / 단점  
- 장점  
1. **Java 언어의 장점**을 갖는다 (객체 지향적, 멀티스레드 개발 등)    
객체 지향적 장점을 통해서 **IOC DI AOP POJO** 같은 객체지향적 개발 원칙을  
Java를 사용하는 Spring에서는 비교적 더 엄밀하게 지킬 수 있다.  

![image](https://user-images.githubusercontent.com/101965836/187692325-91f407b3-27fd-4060-9f15-a556509e7133.png)  
   
2. **무거운 CPU 연산에 유리**하다  
JavaScript를 사용하는 Express에 비해서   
Java를 사용하는 Spring이 CPU 연산 퍼포먼스가 더 좋다.  
  
이는 단순히 **"JavaScript는 싱글쓰레드니까 당연히 느리지 않아?"** 라고 생각할 수 있지만  
사실 **좀 복잡해**서 지금 글을 쓰는 순간에도 왜 그런지 잘 모르겠다.  
그냥 Spring이 무거운 CPU 연산에 강하다 정도로 일단 알아두자.  
    
- 단점  
1. 학습이 어렵다  
동시성 문제 같은 멀티 스레드 문제도 있고  
입문 단계에서 마주치는 스프링 빈이니 각종 애노테이션부터 스프링 MVC 등등  
배울게 좀 많다.  
  
<br>  
  
### Express(Node.js) 장점 / 단점  
- 장점   
1. I/O에 유리하다  
JavaScript는 싱글 쓰레드를 사용해서 I/O에 불리하다고 착각할 수 있다.   
하지만 Node.js가 아키텍쳐단에서 libuv 라는 것을 사용해서  
이벤트 루프 기반으로 논 블로킹 처리를 한다.     
따라서 I/O 작업이 잦아도 멈추지 않고 계속 작업이 진행될 수 있어서 유리하다.  
  
오해하지 말아야 할 것은  
단일 I/O 속도는 Java가 더 빠르다.   
Node.js가 I/O에 유리하다는 말은   
논 블로킹으로 I/O 작업을 하기 때문에  
I/O 때문에 하던 작업을 멈추지 않아도 되어서 Node.js가 I/O에 유리하다는 뜻이다.  
   
2. 오히려 경우에 따라 Spring보다 성능적으로 유리한 부분이 많다  
이 부분이 의외인데   
I/O 성능도 얼추 Java만큼 빠르고  
최초실행시간이나 메모리 사용량 측면에서도 장점이 있다  
  
![image](https://user-images.githubusercontent.com/101965836/187701211-cb504e41-abd0-4b3e-bb95-ff1fcac73aba.png)  
  
I/O 속도도 Java에 비견해 준수하고,   
시작하는데 걸리는 시간이나 메모리 사용량도 Node가 더 유리한것을 볼 수 있다.  
  
이외에도 여러 벤치마크들이 있는데  
항상 Spring이 빠르다, Node.js가 빠르다 이런게 없고  
또 제한적으로 딱 이런 경우에만 Node.js가 빠르다도 아니다.  
다시말하면, 어쩔땐 Spring이 어쩔땐 Node.js가 빠르다.  
  
하지만 최신 앱들의 특징이  
```
1. 다중처리를 동시에 처리하도록 요구된다.
2. 많은 I/O 작업을 수행한다.
3. 단순한 CPU 작업만을 진행한다.
```
이렇기 때문에  
딱 Node.js가 퍼포먼스를 발휘하기에 유리하다.  
  

<br><br><br>  
  
# 이외에 생각해볼점  

### Spring 진영에 최근 WebFlux 라는 것이 도입됐다  
WebFlux는 Node.js같은 방식으로 비동기-논블러킹I/O 처리를 하기 위해 등장했다.  
Node.js와의 차이점은  
Spring WebFlux는 Multi-Thread 방식이라는 점이다.  
  
<br>  
  
### 생산성 측면  
Spring MVC에는 할 일이 많다.  
이것도 해야하고 저것도 해야하고  
엄격한 MVC와 객체지향 설계 원칙부터  
각종 일일이 구현해야 하는 부분들(특히 예외 처리) 때문에  
할 일이 많다.  
  
Node.js는 Spring에 비해서 생산성 측면에서 유리하다.  
생산성 측면에서 유리하다는 뜻은 곧 인건비 문제로 직결된다.  
따라서 스타트업들이 Express를 많이 쓰는 이유중 하나도 생산성 때문일 것이라고 생각한다.  
  
추가로  
Django같은 python쪽이 생산성 측면에서 더 좋다고들 말하는 것 같다.  
이는 python 언어 자체가  
일일이 for문 쓰고 하는 식으로 코딩하는 것 보다  
만들어진 라이브러리를 사용해서 딱 한줄 쓱 쓰는식으로 코딩하기 때문이라고 추측한다.(뇌피셜)    
  
<br>  
  
### Bun이라는 엄청 빠른 JavaScript Runtime이 있다  
Javascript는 근본이 웹브라우저에서 돌아가는 스크립트 언어다.  
따라서 옛날에는 그냥 웹브라우저에서만 동작했었는데  
node.js 같은 runtime들이 웹브라우저 외에서도 JS가 동작하도록 해주기 시작하면서  
서버 처리에서도 JS가 쓰이게 된 것이다.  
  
근데 Node.js가 근본이 있기에 많이 쓰이는것이고 안전한 선택이긴 한데  
속도 측면에서 Bun 이라는 엄청 빠른 JS Runtime도 있다.  
  
![image](https://user-images.githubusercontent.com/101965836/187707234-301a04cb-2b50-4ebf-b611-716d5ca50d32.png)    
[출처 - 노마드 코더](https://www.youtube.com/watch?v=t9924eteb-4)   
  
Node.js에서 34초 걸리는 Test 작업이  
Bun 에서는 2.6초 밖에 안걸린다고 한다.  
  
경우에 따라 다를 수 있지만  
어쨌건 Node.js보다 Bun이 그냥 빠르다.  
  
<br><br><br>  

---


<br><br><br>  

---

# 추가 

---

# 추측 - 왜 JavaScript 가 Java 보다 잦은 I/O에 유리한가?  
  
### Java (Spring)   
: Multi-Thread 기반으로 동시에 I/O 를 처리     
    
### JavaScript (Express라 설명한 Node.js)   
: Single Thread 지만 Non-Blocking I/O 기반으로 동시(?)에 I/O를 처리   

<br>  
   
### Java 가 단일 I/O 처리는 빠르다 
추측해보면   
Java 가 단일 코드 처리 자체는 빠르다는 말에 따라서  
단일 I/O 처리도 더 빠를 것이다.  
  
<br>  
   
### 작은 I/O 가 여럿 일어나면, Java가 더 느리다  
이는 추측해보면    
   
Java 는 Multi-Thread로 동작해서    
오버헤드가 많아지고 별 요청 아닌데도 Thread 하나씩 붙어야 한다는 단점이 있다.   
또한 요청마다 Thread가 생기므로 메모리도 더 많이 쓰게 된다.   
  
반면 JavaScript 는 Non-Blocking 으로 처리해서   
그냥 Event Queue에다가 집어넣기만 하면 되므로 오버헤드와 메모리 사용이 더 적다.     
    
### 참고한 글  
[Is non-blocking I/O really faster than multi-threaded blocking I/O?](https://stackoverflow.com/questions/8546273/is-non-blocking-i-o-really-faster-than-multi-threaded-blocking-i-o-how)    
[Why is Non blocking asynchronous single-threaded faster for IO than blocking multi-threaded for some applications](https://stackoverflow.com/questions/63489917/why-is-non-blocking-asynchronous-single-threaded-faster-for-io-than-blocking-mul)  
