# 특정 유저에게 메세지 보내는 방법  

```java
template.convertAndSendToUser(username, "/topic/destination", messageToSend);
```

위와 같이 convertAndSendToUser 라는 메서드가 있지만,  
문제는 username 을 어떻게 지정해줘야 하는지 이다.  
  
결론부터 말하면 principal.getName() 해서 지정해주면 된다.  
  
하지만 문제가 있는데  
Spring 에 의해서 principal 이 생성되지 않은 경우 (비로그인 즉 anonymous 유저)  
principal 이나 authentication 을 가져오도록 하면 spring 이 null 을 주게 된다.  
  
그래서 Spring WebSocket STOMP 를 사용할 때 특정 유저에게 메세지를 보내기 위해서는   
"어떻게 Principal 을 만들것인가" 가 핵심이다.  
   
<br><br>  
  
# handshake 과정에서 Principal 생성  
  
[참고 - STOMP - convertAndSendToUser 사용법](https://withseungryu.tistory.com/136)   


```java
public class CustomHandshakeHandler extends DefaultHandshakeHandler {
  @Override
  protected Principal determineUser(ServerHttpRequest request,
                                  WebSocketHandler wsHandler,
                                  Map<String, Object> attributes) {
    return new StompPrincipal(UUID.randomUUID().toString());
  }
}
```
위와 같이 HandshakeHandler 를 구현해주고  
  
```java
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {

    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
      registry.addEndpoint("/ws").setAllowedOrigins("*")
        .setHandshakeHandler(new CustomHandshakeHandler());
    }
```
HandshakeHandler 를 등록해준 후  
  

```java
@Controller
@RequiredArgsConstructor
public class WebSocketController {

  private final SimpMessagingTemplate template;
  
  @MessageMapping("/test-principal")
  public void testPrincipal(Principal principal) {
    template.convertAndSendToUser(principal.getName(), "/topic/test", "test-message");
  }
```
이렇게 principal 을 받아서 쓰면 제대로 보내진다.  
  
  
<br><br>  
  
# 왜 이런걸까? 왜 Principal 인가?  
동작 구조는 간단하다.   
HTTP -\> Websocket 으로 Upgrade 하는 Handshake 과정에서 <code>HandshakeHandler</code> 인터페이스를 사용한다.   
  
HandshakeHandler 자체는 doHandshake() 메서드 하나만 있지만   
이를 구현한 AbstractHandshakeHandler 을 보면 doHandshake() 과정이 어떻게 동작하는지 알 수 있다.  
  

### doHandshake() 에서 determineUser() 가 Principal 을 결정  

```java
public abstract class AbstractHandshakeHandler implements HandshakeHandler, Lifecycle {

  ...
  
  @Override
  public final boolean doHandshake(ServerHttpRequest request, ServerHttpResponse response,
    WebSocketHandler wsHandler, Map<String, Object> attributes) throws HandshakeFailureException {

    ...

    Principal user = determineUser(request, wsHandler, attributes);
```
  
```java
@Nullable
protected Principal determineUser(
    ServerHttpRequest request, WebSocketHandler wsHandler, Map<String, Object> attributes) {

  return request.getPrincipal();
}
```

위와 같이 request.getPrincipal() 를 통해서   
사용자의 request 에서 principal 객체를 가져와서 넣어주기 때문에  
protocol 이 upgrade 되는 시점에 principal 객체에 따라서 결정된다.  
  

<br><br>  

### 비회원은 principal == NULL     
    
Spring Security 에서 지정해두길 anonymous 유저인 경우(즉, Guest)    
Argument Resolve 를 통한 principal 주입 시 null 값을 주게 되어있다.     
참고 : [20231109_Controller에AnonymousAuthenticationToken은들어가지않고null이됨.md](https://github.com/PhysicksKim/TIL/blob/main/Spring/Security/20231109_Controller%EC%97%90AnonymousAuthenticationToken%EC%9D%80%EB%93%A4%EC%96%B4%EA%B0%80%EC%A7%80%EC%95%8A%EA%B3%A0null%EC%9D%B4%EB%90%A8.md)  
   
이를 알고 처음에 한 생각은 "비회원도 AnonymousAuthenticationToken 을 반환하도록 바꿀 수 있지 않을까?" 였지만  
이는 간단한 설정 수준으로는 불가능 하다는 것을 알게됐다.  
  
따라서 결국은  
Spring Web Security 에서는 이와 같이 "비회원 principal 은 null" 정책을 지키도록 하고  
Websocket 을 쓰는 상황에서는 Handshake 과정에서 Principal 을 생성해서 제공해 주도록 하는 게 좋다는 생각이 들었다.  
  
<br><br>  

# 비회원 username 입력받기  
  
회원의 경우 principal 의 name 에 unique id 또는 nickname 등을 사용할 수 있다  
그런데 비회원의 경우 principal 이 무작정 UUID 로 들어가면  
관리 측면이나 가독성에서 좋지 못하다.  
  
### UUID 의 용도  
UUID 중복 문제는 0.00000001% 도 신경써야 하는 상황이 아니면 고려하지 않아도 된다.  
따라서 비회원 Principal 에 UUID 로 username 을 넣어준다고 문제가 될 일은 없다.  
하지만 비즈니스 요구사항 때문에 비회원의 경우에도 입력받은 username 을 필요로 할 수 있다.  
그러면 어떻게 handshakeHandler 에게 username 을 전달해줄 수 있을까?  
  
<br>

### HandshakeInterceptor 를 사용해 Map 에 넣어주기  

```java
public interface HandshakeHandler {
	@Override
	public final boolean doHandshake(ServerHttpRequest request, ServerHttpResponse response,
			WebSocketHandler wsHandler, Map<String, Object> attributes) throws HandshakeFailureException {
```
HandshakeHandler 에서 받는 Map<String, Object> 는   
HandShakeInterceptor 가 넣어주는 Map 이다.    
따라서 Interceptor 를 구현해서 Map 에 username 을 넣어주면 된다.  
    
```java
@Override
public boolean beforeHandshake(ServerHttpRequest request, ServerHttpResponse response, WebSocketHandler wsHandler, Map<String, Object> attributes) throws Exception {
    if (request instanceof ServletServerHttpRequest) {
        ServletServerHttpRequest servletRequest = (ServletServerHttpRequest) request;
        HttpSession session = servletRequest.getServletRequest().getSession();
        String userName = (String) session.getAttribute("userName"); // "userName"은 저장된 속성의 이름입니다.
        if(userName == null || userName.isEmpty()) {
            throw new IllegalArgumentException("Websocket 연결 이전에 username 을 설정해주세요");
        }
        attributes.put("userName", userName);
    }
    return true;
}
```
handshake 이전에 http 요청을 통해서 세션에 username 을 넣어주도록 하면 된다.  
> 즉, 웹소켓 연결 전에 client 측에서 닉네임 입력 요청을 받아서 session 에 미리 저장해둔다     
  
