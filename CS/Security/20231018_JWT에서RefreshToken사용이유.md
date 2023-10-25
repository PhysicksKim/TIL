[참고 1](https://stackoverflow.com/questions/38986005/what-is-the-purpose-of-a-refresh-token)  
아주 크게 뇌피셜
  
---  
  
### 처음 내 의문    
1. 해커가 Refresh Token을 탈취하면 답없잖아?
2. Refresh Token ID를 DB에 저장해둔다고? 그냥 Access Token ID를 DB에 저장 해두는 거랑 뭐가 다르지?
3. 근데 ID를 DB에 저장해두면 세션이랑 다를게 뭐야?  

---
     
# 특성 
Access Token : 짧은 유효기간. 인증이 필요한 경우 사용  
Refresh Token : 긴 유효기간. Access Token 발급에 사용  
  
# 왜 나눠두는가? 

## 핵심 장점  
앞서 적은 내 의문에 자문자답해봤다.      
   
### 1. Refresh Token 탈취 해결법 
#### : Refresh Token ID 를 DB에 저장해둔다.    
토큰이 탈취되면
a) 기존 토큰은 파기
b) 새로 Refresh Token 을 발급  
c) DB에 발급한 ID를 넣음         
d) Refresh Token 검증시 DB에서 ID 값과 비교         

<br><br>  
    
### 2. Access Token ID 를 저장해두는 것과 다른점 
#### : {Access Token ID 저장} vs {Access Token + Refresh Token ID 저장} 방식은 DB에 쿼리 날리는 횟수가 달라진다.         
  
a. Access Token ID 저장 예시) Access Token 수명이 30분, 30분동안 사용자가 200번(9초당 1회) Access 요청     
-> DB에 200번 쿼리를 날려서 Token ID 검증 해야함        
   
b. Access Token + Refresh Token ID 저장 예시) 30분에 1번만 쿼리를 날리면 된다.    
  
따라서 안정성 + DB 부하 줄일 수 있다.

<br><br>  
  
### 3. Access는 토큰 방식, Refresh는 세션 느낌으로 한다   

| | 장점 | 단점 |
|---|---|---|
|Session 인증| ID 구별로 보안성 높음 | 중앙 세션 서버 구축 필요, 세션 DB 부하 |
|Token 인증| 인증 서버에 요청 안하고 제3자도 전자서명으로 인증 가능 <br> 매 Access 마다 인증서버에 검증 요청 보내지 않아도 됨 | 순수 토큰 방식으로는 탈취시 무효화 할 수 없음 |  
  
이렇게 Token 방식 Session 방식의 장점을 적절히 섞은게  
Access Token + Refresh Token ID 저장 방식이다.  
  
Access Token 은 수명을 짧게 해서 탈취 되어도 금방 만료되도록 하고,    
Refresh Token 은 DB ID 값을 대조해서 검증하니까, Refresh Token 이 탈취되면 토큰을 무효화 할 수 있다.       
  
따라서 Access Token + Refresh Token ID 저장 방식은  
각각 Token 장점과 Session의 장점을 잘 섞은 방식이라고 할 수 있다.    
  
물론 Session 방식은 탈취시 즉각적인 무효화가 일어날 수 있지만  
Access Token + Refresh Token 방식은 즉각적인 무효화는 어렵고  
토큰 탈취를 감지한 시점부터 Access Token 이 유효한 기간동안 공격이 발생할 수 있다.  
  
> 물론 이 부분도 필요하다면 Access Token의 수명동안 Access 를 전면 금지하도록 할 수 있겠지만   
> 그러면 시스템이 너무 복잡해지지 않을까?

<br>  

> ### 추가) Network 에 자주 노출되는 것도 안좋다
> Network 에 password 같은 민감한 정보가 자주 오가는 것 자체가 안좋은 형태라고 한다.
> 물론 이는 아주 근본적으로 Refresh Token의 이점이라고 보기는 어렵지 않나 싶다.
  
  
<br><br>  
   
### 정리    
Refresh 토큰은 보안성 증대를 위한 것   
보안성 증대를 위해서는 추가적으로 전략 필요 (앞서 말한 대로 Refresh Token ID를 저장해두고 파기할 수 있도록 하기)   
Refresh Token 을 둠으로 인해서 빈번한 Access 작업마다 DB 에서 ID 값과 대조할 필요가 없어지고   
가끔 일어나는 Access Token 재발급 요청때만 아주 단발적으로 DB 에서 ID 값과 대조가 일어나므로     
단순 세션보다 DB 부하를 줄여준다는 장점까지 더해진다.   
따라서 세션 장점과 토큰 장점을 어느정도 융합한 형태이다.   
  
  
