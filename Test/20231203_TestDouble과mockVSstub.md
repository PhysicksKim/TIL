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
   
> 우선    
> "어? 이게 Mock 아냐? 이게 Stub 아냐?"   
> 같은 사전지식 싹 다 잊고 제로부터 시작하자.  
  
Stub : 정해진 응답을 반환함. 검증 시 설정한 응답이 반환 됐는지 여부 체크  
Spy : Stub + 어떻게 호출 됐는지 기록하는 기능 추가    
Mock : 어떤 요청을 몇 번 받을지 지정하고 검증함. stub 기능을 포함하기도 함.        
  
### Stub : 상태 검증 / Mock : 행위 검증
  
- Stub : 정해진 응답 반환 했는가? - 테스트 이후 상태 검증    
- Mock : 3번 호출이 일어났는가? - 테스트 이후 행위 검증  
  
### mockito의 Spy  
Spy는 원래 Stub 객체를 Wrapping 하는 개념인데   
Mockito 라이브러리 에서는 실제 객체를 Wrapping 하는 식으로 쓴다.  
  
### Mock 과 Stub 헷갈리는 이유 : 라이브러리에서 혼용  
Mockito 같은 경우 <code>stub()</code> 이 따로 없고,  
<code>mock()</code> 이 mock 과 stub 을 둘 다 포함하고 있다.    
  
그래서 Mockito 로 <code>mock()</code> 을 먼저 써본 다음 test double 에 대해 배우게 되면  
mock / stub 이 헷갈리게 된다.  
  
> 아니, mock() 으로 만들면 mock 기능 stub 기능 둘 다 쓸 수 있는데?
> 그럼 뭐야? Mock이 stub이야? 아니 mock도 값 반환 되고 verify() 도 다 되잖아?
> 이거 뭐야 mock이 상태 검증도 되는 거 아니야?
  
라고 할 수 있는데, 이는 Mockito 라이브러리 때문에 생긴 오해다.  
Mockito 라이브러리의 <code>mock()</code> 메서드가 Mock 과 Stub 을 둘 다 가진 TestDouble 을 만들기 때문이다   
    
### 라이브러리 마다 다를 수 있다    
mock / stub / spy 에 대한 세부적인 내용은 라이브러리마다 다를 수 있다.    
특히 앞서 봤듯 Mockito 에서는 mock + stub / spy 같이 둘로 구분하고 있기 때문에   
mcok 과 stub 이 헷갈릴 수 있다.    
    
그러므로 라이브러리 마다 mock / stub / spy 가 구체적으로는 다를 수 있다는 점을 인지하고   
큰 그림만 가져가도록 하자.   
특히 mock 과 stub 은, 행위 검증 / 상태 검증 으로 구분되는 게 원칙이지만   
둘을 합친 형태도 있다는 점을 인지해두자.    
