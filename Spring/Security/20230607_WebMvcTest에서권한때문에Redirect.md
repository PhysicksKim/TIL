# 이상하다? 
### : permitAll() 인데 왜 OAuth 로그인으로 redirect하지  
    
```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
  http
    .csrf().disable()
    .headers().frameOptions().disable()
    .and()
      .authorizeRequests()
      .antMatchers("/", "/css/**","/images/**",
              "/js/**","h2-console/**","/profile").permitAll() // index페이지 + 정적 파일 권한
      .antMatchers("/board/**").permitAll() // 게시판 접근은 모든 권한이 접근 가능  
      .anyRequest().permitAll()
    ...
}
```
위 코드의 주석에서도 알 수 있듯  
게시판 접근은 모든 권한이 접근 가능하도록 해놨다.  
  
그런데 이상하게 아래 테스트를 돌리면 302 redirect가 됐다.  
```java
@Test
void mainPage() throws Exception{
    mockMvc.perform(get(BOARD_MAIN_URL)
            .andExpect(status().isOk())
            .andExpect(view().name(BOARD_MAIN_PAGE));
}
```
이렇게 테스트를 돌리기만 하면   
```
MockHttpServletResponse:
           Status = 302
    Error message = null
          Headers = [X-Content-Type-Options:"nosniff", X-XSS-Protection:"1; mode=block", Cache-Control:"no-cache, no-store, max-age=0, must-revalidate", Pragma:"no-cache", Expires:"0", X-Frame-Options:"DENY", Location:"http://localhost/oauth2/authorization/google"]
     Content type = null
             Body = 
    Forwarded URL = null
   Redirected URL = http://localhost/oauth2/authorization/google
          Cookies = []
```
Oauth2 구글 로그 페이지로 자꾸 redirect를 보냈다.  
  

<br><br>

# Guest 권한이면 그냥 Oauth redirect 없이 그냥 통과해도 되는 거 아냐?  
  
이게 의문이었다. 
왜일까?  
  
물론 테스트에다가 권한을 부여해서 해결하는 방법이 있기는 하다만  
localhost로 실행해서 직접 페이지에 들어갈때 크롬 관리자 도구로 분석해보면  
<img width="409" alt="image" src="https://github.com/PhysicksKim/TIL/assets/101965836/64c28588-0f87-42ef-8301-094c89aba3a1">  
  
index page -> board main page 순으로 접근한 로그인데  
redirect 302 가 날아온 적이 없다.  
  
# 결론(추측)  
  
@WebMvcTest 만 달아주고 따로 권한을 설정 안해놓으면  
Spring Security가 "권한 자체가 없네" 하고   
뭐라도 권한 갖고 오라고 Login Page 로 이동시키는 것 같다.  
  
그래서 아래와 같이 @WithMockUser 로 GUEST 권한을 줬고  
이러면 정상적으로 동작한다.  
  
```java
@Test
@WithMockUser(roles = "GUEST") // 게시판은 Guest 권한으로 접근 가능  
void mainPage() throws Exception{
    mockMvc.perform(get(BOARD_MAIN_URL))
            .andExpect(status().isOk())
            .andExpect(view().name(BOARD_MAIN_PAGE));
}
```
  
