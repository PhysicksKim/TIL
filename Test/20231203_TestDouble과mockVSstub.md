# TestDouble 이란  
흔히 Mock 이라고 말하는 것.    
Test 를 위해 만들어 주는 가상의 객체를 말한다.  
  
껍데기만 있을 수도 있고, 개발자가 지정한 대로 아주 간단한 방식으로 동작할 수도 있다.  
  
### TestDouble 이해를 위한 Mock 예시    
Test 에 대해 공부해보면 Mock 을 최소 들어는 보고, 대부분 사용은 해봤을 거다.  
Mock 이란 쉽게 말해서 "이런 요청이 들어오면 이런 값을 돌려보내라" 라는 식으로 만든 가짜 객체를 말한다.  
이러한 가짜 객체를 통해서 '테스트 하고자 하는 객체만 딱' 테스트 할 수 있도록 해준다.  
  
예를 들어, 원래 서비스에서는 고객이 주문할 때마다 주문 내역 메일을 보낸다고 해보자.  
그리고 주문 생성 테스트를 아래와 같이 작성하고 있다. 
```java
@Test
void createOrderTest() {
  // 주문 관련 테스트 코드
  Order order = orderService.createOrder(orderRequest);

  // ...
}
```
```java
@Service 
class OrderSerivce {
  public Order createOrder(OrderRequest req) {
    ...
    boolean result = mailService.sendEmail(...);
    ...
  }
```
이런 메서드가 있을 때, 진짜로 외부 mailService 를 사용하는 것은 부담스럽다.    
왜냐하면, 외부 mail service 가 임시점검이면 테스트에 실패할 수도 있고,  
mail 을 보내는 게 늦어지면 테스트도 늦어지게 되고,  
mail 을 보내는 비용도 문제가 될 수 있다.  
  
하지만 정작 우리가 테스트 하고자 하는 부분은 mailService 가 아니라 OrderService 코드이다.  
그런데 mailService 에 문제가 있으면 덩달아 OrderService 가 실패한다.  
따라서 OrderService 에 대한 테스트에만 집중하기 위해서, mailService 는 가짜 객체를 써서 테스트 하도록 한다.  

여기까지가 TestDouble 의 목적이다.  
  
> 이외에도 다양한 이유가 있을 수 있다.
> 아직 해당 부분 개발이 덜 됐을 수 있고,
> 내부 서비스 이지만 독립적으로 유닛 테스트를 하고 싶을 수도 있다.
> 어찌됐건 Test 에서 가짜 객체가 중요한 것은 마찬가지다.  
  
<br><br>  
  
# Test Double 객체의 종류   
Test Double 에는 5가지 종류가 있다.    
Dummy, Stub, Fake, Mock, Spy  
  
여기서 Dummy , Fake 는 설명이 쉽다.  
  
### 1. Dummy : 그냥 객체 채우기 용도
그냥 진짜 딱 객체 생성에 필요한 인자 채우는 용도로 쓴다.  
동작을 생각하지 않는다.  
예를 들어 mailService 가 생성에 필요하지만, 테스트에 사용하지는 않을 경우  
Dummy 객체를 만들어서 생성에서만 딱 집어넣기 위해서 만든다.  
  
### 2. Fake : 실제 객체와 유사하지만, 훨씬 간단하게 동작하는 객체  
Fake 는 거의 실제 객체처럼, 진짜로 따로 테스트용 Class 를 만드는 것을 말한다.  
  
---

이제 남은 Stub, Spy, Mock 를 설명해야 하는데, 얘네들이 구분이 애매하다.  

# Stub / Spy / Mock
이 셋은 나도 지금 잘 모르겠다.  
솔직히 자신이 없다.  
  
예를 들어 누가 코드를 보여주면서. "자. 이건 Mock 이게 Stub 이게?" 라고 물어본다면  
100% 정답을 맞출 자신이 없다는거다.  
  
특히, Java 기준 Spring + Mockito 로 Test 코드를 만든다면,  
Stub 과 Mock 은 둘다 <code>mock()</code> 메서드를 사용해서 만든다  
검증도 마찬가지로 verify() 라는 mock/stub 과 별개인 것 처럼 보이는 메서드를 사용하기 때문에  
mock 과 stub 의 구분이 쉽지 않다.  
  
단지 Mock 과 Stub 은 개념상의 차이가 있는 것 같다.  
  
## 아마도... 이런 말인가?  
지금 내 이해는 이렇다.   
  
- Stub : 상태 검증. 가짜 객체 + 호출시 지정된 값 반환       
- Spy : stub + 변화 기록.    
- Mock : 행위 검증.  
  
근데 상태 검증과 행위 검증이라는 말 자체가 잘 안와닿는다.  
뭔소리지?  
설명을 봐도 내가 어디부터 이해를 못했는지 감이 안잡힌다.  
좀 더 알아봐야할듯. 
