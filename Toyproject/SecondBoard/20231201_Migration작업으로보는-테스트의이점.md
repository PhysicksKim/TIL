# 테스트 덕을 봤다  
Boot 2 -> 3 Migration 작업 중, Controller Test 에서 에러를 발견했다.  
이후 여러 분석 결과 Boot 3 + Intellij 환경에서 Method Parameter Name 을 사용하는 Reflection 작업에 에러가 있음을 발견했다.  
  
<br><br>  

# 서론 - Migration 하게된 배경  
  
며칠 전 SecondBoard 의 버전을 Spring Boot 2.7.4 -> 3.2.0 으로 업데이트하는 Migration 작업을 진행했다.    
아마도 실무에 뛰어들면 아무리 최신이여봤자 Boot이 2버전이지 않을까? 라고 생각했었다.      
  
그런데 이제 심지어 [Start.Spring.io](https://start.spring.io/) 에서 2.X 는 띄우지도 않는 지경에 이르렀다.     
이제는 진짜 3.X + java 17 에 적응해야 한다 라고 생각해서   
Boot 2 -> 3 으로 Migration 작업을 진행했다.  
  
Migration 전부터 걱정이 많았는데, Test 믿고 진행했다.  

<br><br>  

# 본론 - Controller Test 에 에러 발생  
Migration 작업은 아래와 같은 순서로 진행했다.     
  
1. Dependency 해결 (Gradle Build)
2. Spring App Run 컴파일 가능하도록 해결
3. Test Code All Pass 

<br>

## 1. Dependency 해결  
Java 17 로 변경 작업과 Boot 3 + Jakarta Validator, hibernate validator 버전 매칭 같은 문제 등을 해결했다.  
어려움 없이 잘 됐다.  

<br>
  
## 2. Spring App Run 컴파일   

- ### Spring Security 6.0 변경점  
먼저 Spring Security 버전이 올라가면서 Deprecated 된 Config 방법들을 해결해야 했다.  
그런데 자꾸 Config 가 적용이 안되는 문제를 겪어서 왜인가 확인해보니  
<code>@EnableWebSecurity</code> 어노테이션에 <code>@Configuration</code> 이 빠지게 되면서, 따로 @Configuration 을 적어주도록 바뀌게 된 것이었다.  
이거 말고는 config를 작성하는 방식이 method chaining -> 함수형 으로 바뀌었는데  
이건 별로 안어려웠다. 그냥 간단히 수도 코드로 구조를 나타내면 아래와 같다.  
  
```java
@Bean
SecurityFilterChain filterChain(HttpSecurity http) {

  http
        .설정분류1(configurer -> configurer
                .methodChain1(설정값)
                .methodChain2(설정값)
        )
        .설정분류2(configurer -> configurer
                .methodChain1(설정값)
                .methodChain2(설정값)
        );
}
```

  <br>
  
- ### Thymeleaf 3.0 변경점  
Boot 3 에 맞춰 Thymeleaf 버전도 3.0 으로 올라가게 됐다.  
   
이전 버전 Thymeleaf 에서는 Model에 별다른 작업을 하지 않아도 기본적으로 Servlet 객체를 얻어올 수 있었다. <code>#httpServletRequest</code>   
하지만 3.0 부터는 Servlet 객체를 기본적으로 가져올 수 없게 됐고,    
이에 따라서 메뉴바에서 현재 페이지가 어디인지 표시해주던 template code 를 변경해야 했다. <code>{#httpServletRequest.requestURI}</code>   
  
이것도 Interceptor 라던가 ControllerAdvice 또는 Custom Annotation 추가 하는 식으로 해결할 수 있다.  
나는 일단 간단히 <code>GlobalControllerAdvice</code> 로 model에 꼬박꼬박 request URL을 넣어주는 식으로 대충 해결하고 넘어갔다  
일단은 이 부분만 해결하면 Run/Build 는 당장에 되니까  
대충 구동만 가능한 수준으로 두고 넘어갔다.  

<br>
  
## 3. Test Code All Pass  
Controller쪽 Mock Test 에서 계속 문제가 발생했다.  
에러 메세지를 구글링 해보니까 Reflection 문제가 생겨서 Java Compile 과정에 "-parameters" 옵션을 추가해 주라고 한다.  
그런데 Gradle에 추가해도, Intellij 에 추가해도 자꾸 똑같은 에러가 난다.  
  
그래서 이리저리 해보니까  
Spring Boot 3 에서 Project Build Tool 을 Intellij 로 설정하면 이런 에러가 발생하는 것이었다.  
Spring Boot 2로 내려도 해결되고, Project Build Tool 을 Gradle(Default)로 둬도 해결이 된다.  
딱 Boot 3 + Intellij Build 에서만 안된다.  
  
사실 그냥 Project Build Tool 의 Default 가 Gradle이고, Intellij 로 꼭 Build 해야 하는 필요도 없으니까, 그냥 대충 딱 하고 넘어가도 된다.  
    
근데, "이거 버그 아냐?" 라는 생각이 듦과 동시에    
"내가 Intellij IDE 의 버그를 찾은건가?" 라는 생각에 오히려 가슴이 두근거렸다.  
그리고 곧바로 이리저리 테스트 한 끝에 '이거 진짜 버그인가봐' 싶어서   
잘 정리해서 Issue 를 남겼다.    
  
자세한 문제에 대해서는 별도로 [20231128_컴파일옵션으로인한에러발생.md](https://github.com/PhysicksKim/TIL/blob/main/Toyproject/SecondBoard/20231128_%EC%BB%B4%ED%8C%8C%EC%9D%BC%EC%98%B5%EC%85%98%EC%9C%BC%EB%A1%9C%EC%9D%B8%ED%95%9C%EC%97%90%EB%9F%AC%EB%B0%9C%EC%83%9D.md) 여기에 다 설명해뒀다.  
  
<br>

## Issue 남긴 후 소감  

### Q. "본인의 오픈소스 기여 경험이 있다면 이를 기술하시오"   
### 이전 생각 : "에이 이거는 진짜 고수들이나 오픈소스 코드 뒤져보고 이슈 리포트 하는 거 아니야?"   
라고 생각했었는데 이걸 했네!        
  
Issue 재구현도 가능하고,   
뭐가 문제인지 추측도 해주고(Java Compile Reflection 과 Boot 3 + Intellij Build 문제인듯), 
Mac 에서도 Windows 에서도 동일한 문제가 나니까 테스트 케이스와 환경도 확실히 적어줬다.  
  
오우 굳;   
처음으로 쓸모있는 개발자가 된 것 같고, "내가 감히 Intellij에???" 같은 느낌도 들어서 설랜다   
사실 별 거 아니긴 한데 그래도 두근거렸다. \<쓸모있는 개발자\> 칭호의 첫 걸음 같은 느낌이었다.       
  

<br><br>  

# 결론 - 테스트 덕에 잡았다      
만약 테스트가 없었다면    
Board 관련 기능을 만들고 테스트 하다가    
자꾸 Method Parameter 에 Request 값이 안들어오는 것을 보고   
"아 이거 왜이러지" 라면서 내가 추가했던 기능에서 문제를 찾으려 했을 것이다.    
    
근데 테스트가 있던 덕분에    
Migration 작업에서 발생한 문제임을 캐치했고    
이후 Boot 2 -> 3 과 Intellij Build 호환성 문제임을 곧바로 (하루만에) 캐치할 수 있었다.    
     
테스트가 없었다면 Boot 2 -> 3 문제인지도 몰랐을텐데 말이다.    
    
사실 예전에 Controller Mock Test는 진짜 쓸모없어 보였다.    
   
"""
ontroller Test 는 왜 하는거지 모르겠네.  
진짜 간단한 값 비교 작업밖에 안하잖아?  
view return이나 확인하고, Service Mock 에는 뻔한 return 코드나 넣고, 
앞에서 만든거랑 똑같은 int 값 equals 밖에 안하는데, 
이런 테스트를 왜 하는거지? 
"""
   
라고 생각했었다.    
    
근데 이유를 Migration 과정에서 곧바로 절감해 버렸다.    
Controller 테스트가 이런 곳에서 힘을 발휘하다니.    
"헛된 테스트 코드가 아니었구나!" 싶어서 기분이 좋다.  
