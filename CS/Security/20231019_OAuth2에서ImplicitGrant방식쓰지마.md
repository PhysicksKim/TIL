# Implicit Grant 방식은 안전하지 않으니 쓰지말자  

> implicit grant 방식이 뭔지에 대해서는 생략  

### 안전하지 않은 이유  
Token 을 User를 통해서 전달해주는데 URI에 Token 값을 담아서 전달한다.  
중요한 Token 문자열을 URI 에다가 담아서 Client 에게 전달한다는 자체가 안전하지 않다.  
왜냐하면 User 의 보안이 완전하지 않다면 URI에 담긴 값들은 공격자에게 쉽게 노출될 수 있기 때문이다.  
기본적으로 URI에는 민감한 값은 담지 않아야 하는데 Implicit Grant 방식은 URI에 토큰 값 자체를 담아버리니까 당연히 안전하지 않다.  



<br><br><br>  

# Implicit Grant 검색해서 찾아보다가 낚여서 해맸네 
  
내가 이해를 잘못한건지 모르곘는데   
Implicit Grant 방식을 마치 "보안이 안좋으면 이거 쓰세요" 라는 식으로 설명한듯한 글들이 있다.  
내 생각에 Implicit Grant 방식은 "단순하지만 너무 안전하지 않으니까 절대 쓰지 마세요" 라고 꼭 말해줘야 할 것 같은데 말이다.  
  
## Implicit grant 방식에 대한 이상한 설명    
  
OAuth2 의 방식에 대해서 찾아보면 이렇게 설명하는 글들이 흔히 퍼져있다.  
> 자격증명을 안전하게 저장하기 힘든 클라이언트(ex: JavaScript등의 스크립트 언어를 사용한 브라우저)에게 최적화된 방식입니다.
  
"안전하게 저장하기 힘들면 이 방식(Implicit Grant)을 쓰세요" 라는 의도로 쓴 글인것 같은데  
사실 Implicit Grant는 그냥 애초에 고려사항에 넣으면 안된다. 그냥 절대 이거 쓰지마! 수준이다.    
[Why you should stop using the OAuth implicit grant!](https://medium.com/oauth-2/why-you-should-stop-using-the-oauth-implicit-grant-2436ced1c926)   
> Well, in the old days (back in 2010) browser-based apps were restricted to sending requests to their server’s origin only.  
> So there was no way to call an authorization server’s token endpoint at a different host.  

위 article을 보면 알 수 있는데, 그냥 앞선 문장에서 말하는 "자격증명을 안전하게 저장하기 힘든 클라이언트" 라는건 진짜 옛날이야기다.  
근데 아직도(불과 올해에 적힌 블로그 포스트도, 2~3년전에 적힌 포스트에서도) 저 문장 그대로 가져다 쓰는 글들이 있다.  
  
대체 왜 Implicit Grant 를 이렇게 모호하게 설명해서  
마치 "안전하지 않으면 이 방식을 쓰면 돼" 라는 느낌을 주는지 모르겠다.    
  
> 위 문장을 그대로 복사해서 구글 검색해보면, 그대로 복사 붙여넣기 한 블로그들이 여럿 나온다.  
  
<br><br>  
  
### 문장에 대해 다시 보자  
  
> 자격증명을 안전하게 저장하기 힘든 클라이언트(ex: JavaScript등의 스크립트 언어를 사용한 브라우저)에게 최적화된 방식입니다.  
  
아무리 생각해도 오해를 불러 일으킬만한 문장이다.    
  
- "자격증명을 안전하게 저장하기 힘든" : 개발하시는 분이 안전에 확신이 없으시면   
- "~ 클라이언트(ex: JS등 사용한 브라우저)" : JS 안쓰는 브라우저가 어딨는데? Vanila JS 말하는건가? Vanila JS라고 항상 안전하지 않은가?    
- 최적화된 방식입니다 : 최적화 됐다면 이 방식이 안전을 보장해주는거야?    
  
진짜 아무것도 모르는 상태에서   
"그냥 한번 OAuth2 써봐야지" 라고 검색하다가 저 말을 봤다면,   
"아~ Implicit Grant 방식이 안전하지 않은 어플리케이션에게 적합한 방식이구나!" 라고 오해했을 것 같다.   
    
<br><br><br>   
   
## 내 생각에 저 글은 이렇게 고쳐 써야한다.    
     
> Implicit Grant 방식은 어쩔 수 없이 여러 보안장치를 쓸 수 없는 상황임에도 OAuth2 토큰을 사용해야 할 때 사용하는 방식입니다.  
   
그리고 아래와 같은 설명을 필수적으로 해줘야 한다.   
   
> Implicit Grant 방식은 어쩔 수 없이 URI에 Token을 담아서 Client 에게 전달해야 할 때 사용하는 방식입니다.  
> URI 에 Token 값이 담겨있으니 Token이 노출될 위험이 매우 높습니다.  
> 따라서 이 방식은 다른 OAuth 2 방식을 쓸 수 없는 극한의 상황에서만 사용하세요.  
> (이 방식에 적용할 수 있는 Token 탈취 방지 보안은 Token 만료 기간을 아주 짧게 하는 수 밖에 없습니다)  
   
<br><br>   
