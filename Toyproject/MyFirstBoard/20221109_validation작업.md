- Validation 작업 정리  
validation 작업은 빈번하게 일어나는데, validator를 쓰거나 annotation으로 간단히 해결할 수 있기도 한다  
주로 간단한 null또는공백금지, 최대값 최소값 같은 경우 annotation을 쓰고  
좀 더 복잡한 로직이 필요한 경우 Spring이 제공하는 validator를 써서 AOP 방식으로 해결할 수 있다  
그리고 이 과정에서 bindingResult에 error가 알아서 담기고  
에러 메세지 또한 errorcode에 따라서 적절히 생성된다.  
  
빈번하게 일어나는 validation에 대해서   
  
1. validation 방식 정리  
2. error 메세지 전달, 메세지 코드 정리  
3. thymeleaf 에서 표시하는 방법 정리  
  
위 3가지로 나눠 정리하겠다  


