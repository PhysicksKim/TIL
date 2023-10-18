[참고 1](https://stackoverflow.com/questions/38986005/what-is-the-purpose-of-a-refresh-token)  
     
# 특성 
Access Token : 짧은 유효기간. 인증이 필요한 경우 사용  
Refresh Token : 긴 유효기간. Access Token 발급에 사용  
  
# 왜 나눠두는가? 

### 기본 전략  
Access Token 이 탈취 되어도 금방 만료되도록 한다.  
Refresh Token 이 탈취됨을 감지하면, Token을 파기시켜버리고 사용자가 다시 로그인해서 토큰을 새로 발급하도록 한다.  
  
## Refresh Token 파기 전략
토큰을 식별할 방법(토큰 자체 저장, 토큰에 ID 값 부여해서 저장 등)을 마련해두고
서버 메모리나 DB에 Refresh Token 을 저장해둔 다음
파기가 필요할 경우 저장된 Refresh Token 을 새로 발행해두고 저장해둔 토큰 식별자를 바꿔주면 된다.   
  
## Q. 그냥 Access Token에 Refresh ID 를 넣어도 되지 않는가?  
위 질문대로 한다면 Refresh 동작 자체는 똑같이 구현할 수 있다.  
하지만 문제점이 있다.    
Access 토큰이 오고가면서, Access 동작과 무관한 RefreshID 값이 Network 상에서 계속 노출된다는 점이다.  
  
따라서 Access Token 과 Refresh Token을 분리해서   
자주 오고가는 Access Token 에는 최소한의 유저 정보만 두는 게 좋다.  
  
