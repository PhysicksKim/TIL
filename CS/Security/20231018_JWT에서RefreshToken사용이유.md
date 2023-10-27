[참고 1](https://stackoverflow.com/questions/38986005/what-is-the-purpose-of-a-refresh-token)    
[참고 2](https://velog.io/@park2348190/JWT%EC%97%90%EC%84%9C-Refresh-Token%EC%9D%80-%EC%99%9C-%ED%95%84%EC%9A%94%ED%95%9C%EA%B0%80)   
[참고 3 - Where to store JWT refresh tokens](https://security.stackexchange.com/questions/271157/where-to-store-jwt-refresh-tokens)    
[참고 4 - Where to store acces and refresh tokens](https://www.reddit.com/r/node/comments/12ailfn/where_to_store_acces_and_refresh_tokens/)  
  
뇌피셜 아주 크게 포함  
  
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

# Refresh Token 유의사항

## 1. Access Token 만료시 Refresh Token 도 재발급  
   
Refresh Token 을 1회용으로 두는 것이다.    
이렇게 하면 단발성 Refresh Token 탈취에 대응할 수 있다.  
  
Refresh Token을 1회용으로 두더라도 앞서 말한 장점들을 지킬 수 있다.    
Refresh Token 재발급 비용이 들겠지만, 그렇게 크지는 않을 것 같다.   
(예를 들면 접속자 1명당 30분에 1번 요청 수준이니까)  

실제로 ietf 에서도 매 Access Token 재발급 마다 Refresh Token 재발급을 권장한다.  
  
> [ietf - 8. Refresh Tokens](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps-00#section-8)  
> If an authorization server does choose to issue refresh tokens to  
> browser-based applications, then it MUST issue a new refresh token  
> with every access token refresh response.  
  
### 왜  
   
> Doing this mitigates the risk of a leaked refresh token,   
> as a leaked refresh token can be detected if both the attacker and the legitimate client attempt to   
> use the same refresh token.  
  
Access Token 재발급에 Refresh Token도 같이 재발급 하면   
공격자가 Refresh Token 을 탈취해서 재발급 요청을 했음을 감지할 수 있다.        
  
<br>  
  
## 2. Token을 어디에 저장해야할까   
   
[참고 - Where to store the refresh token on the Client?](https://stackoverflow.com/questions/57650692/where-to-store-the-refresh-token-on-the-client)    
  
http-only 쿠키에 저장하길 권장한다.  
일반 쿠키, 로컬 스토리지는 javascript로 접근할 수 있으므로 권장하지 않는다.  
  
<br>  

## 3. Refresh Token 을 Server Side의 DB에 저장 할 때, 해싱해서 저장한다  
해커가 DB 에서 Refresh Token을 탈취할 위험 또한 존재한다.   
그래서 Refresh Token 을 저장할 때에는 해싱을 거쳐 DB에 저장하는 게 좋다.  

어차피 유효성 검증에서는 받은 Refresh Token 을 해싱해서 검증하면 된다.  
(Spring 에서 Password Encoder 를 활용해서 검증하면 된다. 비밀번호 검증이랑 매커니즘이 똑같으니까.)    
  
<br>  

## 4. Client Side 에 Refresh Token 저장은?  
크게 2가지 방법이 있다.  
  
1) HttpOnly 쿠키 저장 (HTTP only + Secure 쿠키)  
2) Local Storage 에 저장

| |HttpOnly 쿠키|Local Storage|
|---|---|---|
|장점|탈취 위험 비교적 적음|필요할 때에만 Request 에 담아 보낼 수 있음 <br> (네트워크 노출 적음)|
|단점|매 Request 마다 자동으로 담아 보내짐 <br> (네트워크 노출 많음)|탈취 위험이 비교적 큼|

> XSS 공격에 대한 위험은 어차피 두 방식 다 존재한다.  

### Server Side Rendering(SSR) App 에서는 Token 다루기가 까다롭다  
Client Side Rendering(CSR) 에서는 어차피 공통된 JS 코드를 넣기도 쉽고, 또 이를 잘 지원한다. (ex. React)  
따라서 CSR 에서는 Token 처리 로직을 모든 페이지에 넣어두기 쉽다. SPA 같은 경우 더욱 적합하다.  

반면 서버 사이드 랜더링의 경우에는 애매해진다.  
Thymeleaf 같은 경우 모든 페이지에 공통으로 JS 를 넣어주는 기능이 없다.  
따라서 개발자가 모든 페이지에 다 Token 처리 해주는 JS 를 넣어줘야 하는데  
실수로 누락된다면 문제가 생긴다.  
  
따라서 SSR 에서는 HttpOnly 쿠키를 쓰는게 더 편하기도 하고  
아예 Token 방식의 단점이 너무 커지면 Session 방식으로 돌아가는 것도 고민해볼만 하다.  
  
<br><br>  

# 좋은 인터페이스 설계 필요해 보임  

생각보다 Token 의 구현 방식이 세부적으로 들어가면 다양해지는 것 같다.    
Token 저장 DB가 바뀔 수 있고    
Token 방식이 바뀌어서 검증 로직이 바뀔 수 있다.  
심지어 Token 방식에서 Session 방식으로 바뀔 수 있다.  
따라서 잘 추상화 된 설계가 중요해 보인다.  

<br><br>  
   
### 정리    
Refresh 토큰은 보안성 증대를 위한 것   
보안성 증대를 위해서는 추가적으로 전략 필요 (앞서 말한 대로 Refresh Token ID를 저장해두고 파기할 수 있도록 하기)   
Refresh Token 을 둠으로 인해서 빈번한 Access 작업마다 DB 에서 ID 값과 대조할 필요가 없어지고   
가끔 일어나는 Access Token 재발급 요청때만 아주 단발적으로 DB 에서 ID 값과 대조가 일어나므로     
단순 세션보다 DB 부하를 줄여준다는 장점까지 더해진다.   
따라서 세션 장점과 토큰 장점을 어느정도 융합한 형태이다.   
  
그리고 Refresh Token 자체가 장점도 많지만 보안 위험도 높으니까  
주의 사항을 잘 체크하고 보안에 신경써서 구현해야 한다.  
