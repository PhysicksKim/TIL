# FrontEnd 개발 CORS 해결
CORS 설정은 Server 측에서 Access-Control-Allow-Origin 헤더를 설정해줘야 해서   
FrontEnd 개발 환경에서는 해결이 까다롭다.  
  
특히나 Server측 헤더를 설정해줄 수 없는 대표적인 상황이 "외부 API 를 사용하는 상황"이다.  
시나리오로 이해해보자.  

## NAVER api 이용 예  
naver의 chzzk api 를 이용하면 아래와 같은 URL 로 request 를 보낸다.  
```
https://api.chzzk.naver.com
```
    
그리고 front 개발은 <code>localhost:3000</code> 에서 이뤄진다.  
  
따라서 CORS 문제가 발생한다.   

- Server : <span>https:/</span>/api.chzzk.naver.com
- Client : <span>http:/</span>/localhost:3000

이를 해결하려면 원론적으로는 Server(NAVER) 측에서 header 를 설정해줘야 하므로   
다른 방법을 생각해야 한다.  
  

<br><br>  

# 대표적인 방법 3가지  
1. 서버 측 header 설정  
2. CORS proxy server 사용
3. 확장프로그램 사용
  
# 간략한 설명  

## 1. 서버 측 header 설정  
api 서버에서 allow-origin 으로 <n>http:/<n>/localhost:3000 을 허용 해줘야 한다.  
이건 API 서버측(NAVER)에서 해줘야 하므로, 현실적으로 불가능하다.  
  
## 2. CORS proxy server 사용  

### 방법 1) CORS proxy 서버 구현  
따로 proxy 서버를 만들고, proxy 서버에서 api를 fetch 한 다음 결과를 client 에게 전달해줄 수 있다.  
예를 들어 <span>https:/<span>/myproxy.com 을 CORS proxy 로 사용한다면  
API Fetch 를 <span>https:/</span>/api.chzzk.naver.com 로 하는 게 아니라 myproxy.com/userinfo 같은 식으로 해주고  
proxy server 측에는 이에 대응되는 proxy 응답을 구현해주면 된다.    
  
다만 이 방식은 결국 서버를 또 구현해줘야 한다는 부담이 있다.  
  
### 방법 2) http-proxy-middleware 사용  
개발 환경에서는 http-proxy-middleware 를 통해서 간단하게 proxy 서버를 띄울 수 있다.   
  
client app 에서 곧바로 api 서버를 호출하면 CORS 에러가 발생하는데  
이걸 중간에 webpack dev server 를 활용해서 대신 특정 URL로 request를 날려주고 응답을 그대로 client 로 전달해주기만 한다.    
  
> 후술하겠지만, library 에서 고정된 주소로 api 를 요청하는 경우, http-proxy-middleware 로 해결할 수는 없다.  
> 더불어 http-proxy-middleware 는 dev 환경에서만 사용가능하므로,  
> 실제 서비스에서는 어차피 proxy 서버를 써야한다.    
  
## 3. 확장프로그램 사용  
chrome extension 중에서 CORS를 disable 해주는 확장 프로그램이 있다.  
이 방법에 대해 자세히 알아보진 않았다.  
추측해보면   
장점은 간편하게 확장프로그램으로 해결할 수 있다는 점이고,  
단점은 CORS 옵션을 확장프로그램에 의존한다는 측면에서 dev 환경의 일관성 유지에 어려움이 있다는 단점이 있다.    
  
<br><br>  

# 문제발생 : Library 가 고정된 주소로 API 호출
비공식 chzzk api 라이브러리를 사용하던 중 문제가 생겼다.   
대부분의 경우 라이브러리는 fetch 데이터의 처리 작업을 도와주는 정도로 동작한다.  
반면 비공식 chzzk api 라이브러리의 경우  
request fetch 작업을 library 가 직접 날리고, 처리 작업도 library가 직접 한다.  
이에 따라서 CORS 문제가 발생한다.  
  
만약 fetch 작업을 client 에서 한 다음   
fetch 결과 라이브러리에 주입하는 식으로 동작했다면 이게 해결 될텐데    
library 에서 직접 fetch 하는 구조로 동작하기 때문에   
proxy server 에서 chzzk 라이브러리를 사용해 동작하도록 구현해야 한다.  
따라서 단순히 http-proxy-middleware 를 사용해 해결할 수 없고  
직접 node backend 를 구축하고 node backend 에서 chzzk 라이브러리를 사용해야 한다.  
  
<br><br>  

# 대안 : Chrome Extension  
크롬 확장프로그램은 일반 웹 페이지와 다르게 좀 더 유연한 보안정책을 사용한다.  
확장프로그램에서는 SOP에 따르지 않고  
허용하는 Origin 을 Client 측에 지정해줄 수 있다.  
  
아래의 <code>manifest.json</code> 예시와 같이,   
permissions 옵션에 허용하고자 하는 Origin 을 입력해주면 된다.  

```
{
    "name": "my extension",
    "description": "how to allow other origin in extension",
    "version": "0.0.1",
    "manifest_version": 3,
    "permissions": "https://api.chzzk.naver.com",
    ...
}
```
  
따라서 간단한 앱에는 크롬 확장프로그램을 활용해서 개발해주면서 CORS를 간단히 해결할 수 있다.  
특히 Toy Project 를 하다가 CORS 문제에 마주쳤는데, proxy server 를 따로 두기에는 과하다 싶을 수 있다.    
이런 경우에는 확장프로그램으로 간단히 해결할 수 있을지를 생각해봐도 좋을 것 같다.      
확장프로그램에서는 보안정책을 좀 더 자유롭게 설정할 수 있기 때문이다.  
  
