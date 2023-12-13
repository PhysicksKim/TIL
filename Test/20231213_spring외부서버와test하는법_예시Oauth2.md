[스프링에서 추천하는 okhttp mockwebserver](https://docs.spring.io/spring-framework/reference/web/webflux-webclient/client-testing.html)  
  
# 외부 서버와 테스트란?  
  
## 예) OAuth2 테스트   
Google (OAuth2 Provider) - My App (OAuth2 Client) - User  

여기서 OAuth2 Provider 를 어떻게 테스트 할지가 관건이다.  

### OAuth2 유저 정보를 가져오는 경우 - Authorization Code Type Flow 
  
1. (User) OAuth2 Provider 에게 받은 Authorization Code 를 My App 에 전달함  
2. (My App) 전달 받은 Authorization Code 를 Google 에 보내면서 User 정보를 요청한다  
3. (Google) Authorization Code 가 올바르면 User 정보를 응답한다
4. (My App) Google 의 응답 정보를 받아서 처리한 후 User 에게 HTTP Response 를 전달해준다.
  
여기서 Google 부분을 Mocking 해야 하는데,  
이 부분이 어렵다.    
  
## 외부 서버 Mocking 어려움  
OAuth2UserService 는 'OAuth2UserRequest 를 받아서 처리한 후, Oauth2 Provider 에 해당 유저 정보를 요청하는 Request 를 전송' 하는 방식으로 동작한다.  
따라서 OAuth2UserService 를 테스트 하는 코드에서 필연적으로 **"외부 서버로 Request 를 보내게 되는데"**   
이 부분 mocking 이 까다로워 진다.    
  
좀 더 자세히 설명하면, OAuth2UserRequest 가 OAuth2 Provider URI 를 포함하고 있는데  
URI 는 원하는 대로 지정해줄 수 있을 지언정, 이를 외부로 Request 보내는 동작은 Mocking할 수 없다.  
  
물론 이 부분을 객체 주입을 통해서 해결할 수 있다.    
Request 보내는 동작을 수행하는 객체(Request Operator라 하자)를 별도로 분리한 다음  
OAuth2UserService 생성 시점에서 Request Oerator 를 Mocking 해서 주입하는 식으로 만들 수 있다.  
이는 TDD 관점에도 부합하는 좋은 구조가 맞다.   

하지만, 이러한 mocking 을 위해서 프레임워크에서 제공하는 많은 기본 객체들을 Wrapping 하는 객체를 만들어야 하는 번거로움이 발생한다.   
결과적으로 Test 하나를 위해서 엄청나게 많은 객체를 만들어야 한다.  

### 외부 서버 Mocking이 필요하다   
결국 프레임워크/라이브러리를 쓰다보면, 필연적으로 외부 서버 통신을 필요로 하는 것들을 마주치게 되는데  
이 때 테스트에 적합한 형태로 어떻게 만들지 결정해야 한다.  
  
1. 테스트에 용이한 형태로 Wrapping 할건가  
2. 외부 서버 자체를 Mock 서버로 띄울텐가  
  
TDD 관점에서는 1번 방식이 올바를 수 있다.    
하지만 2번 방법으로 심플하게 해결할 수도 있다.  
  
Spring Security 팀에서도 2번 방법을 통해서 테스트를 작성하고 있다.  [spring-security::DefaultOAuth2UserServiceTests](https://github.com/spring-projects/spring-security/blob/main/oauth2/oauth2-client/src/test/java/org/springframework/security/oauth2/client/userinfo/DefaultOAuth2UserServiceTests.java)  
Spring 문서에서도 "WebClient 를 사용하는 경우 OkHhtp 의 MockWebServer 를 사용한다" 라고 설명하고 있다. [링크](https://docs.spring.io/spring-framework/reference/web/webflux-webclient/client-testing.html)  
  
그러므로 외부 서버 Mocking 라이브러리를 사용해서 테스트를 작성하면, 좀 더 다양한 객체에 대해 테스트를 수행해볼 수 있다.  
  
<br><br>  
  
# 간단한 OkHttp MockWebServer 소개    
MockWebServer 사용법은 간단하다.  
  
1. 테스트 전 : Mock 서버 Open (@BeforeEach)    
2. given : Mock 서버 URI 설정, 응답 주입  
3. when : Mock 서버에 요청 보냄 (테스트 하고자 하는 Service 들이 보냄. Service 에는 Mock 서버 URI 주입해줘야 함)  
4. then : 응답 검증
5. 테스트 후 : Mock 서버 Close (@AfterEach)  

### 유의할 점  
  
- 응답은 URI 로 요청이 들어오면, 요청 내용과 무관하게 항상 "순서대로 응답을 반환" 한다.    
- 서버 URI 를 MockWebService 에 설정해주고, 테스트 하고자 하는 Service 에도 URI를 주입해줘야 한다.
- 만약 Service 가 "고정된 URI 로 요청을 보내도록 설계되어" 있다면, URI를 주입받도록 구조를 바꿔야 한다.
  
## 코드 예시 

```java
@DisplayName("Mock Server URI 로 요청을 보내고 응답을 받는다")
@Test
void MockWebServer_getBasicResponse() throws IOException {
  // given
  this.server.enqueue(new MockResponse()
          .setResponseCode(200)
          .setBody("hello, world!"));
  String url = this.server.url("/hello").toString();

  OkHttpClient client = new OkHttpClient();

  // when
  Request request = new Request.Builder()
          .url(url)
          .build();
  Response response = client.newCall(request).execute();

  // then
  assertThat(response.code()).isEqualTo(200);
  assertThat(response.body().string()).isEqualTo("hello, world!");
}
```
