[출처](https://www.inflearn.com/questions/77417)  

# 작성 계기  
Inflearn 스프링 MVC 공부하다가 ItemRepository랑 ItemService가 거의 비슷한데 왜 분리하는지 모르겠어서 적음  

# Service와 Repository 차이 
Repository : DB 접근 코드가 담겨진 곳
Service : 비즈니스 로직이 담겨진 곳  
  
# 분리하는 이유  
이렇게 분리해두면  
DB 관련 문제가 생기면 Repository 코드를 보면 되고  
로직 관련 문제가 생기면 Service 코드를 보면 된다.  

또한 DB를 교체해야하는 경우에 Repository만 또 새로 구현해주면 된다    
그리고 service에 있는 비즈니스 로직 코드는 그대로 두면서도 repository를 바꿔 끼울 수 있게된다(DI로 repository를 가져다 쓰기 때문)  
