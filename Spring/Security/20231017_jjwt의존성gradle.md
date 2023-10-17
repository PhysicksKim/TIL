# JWT 의존성 

jjwt 의 api, impl, jackson 3가지를 추가해줘야 한다.  
  
1. [jjwt impl](https://mvnrepository.com/artifact/io.jsonwebtoken/jjwt-impl)   
2. [jjwt api](https://mvnrepository.com/artifact/io.jsonwebtoken/jjwt-api)  
3. [jjwt jackson](https://mvnrepository.com/artifact/io.jsonwebtoken/jjwt-jackson)   
  
```
implementation 'io.jsonwebtoken:jjwt-api:0.11.5'
runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.11.5'
runtimeOnly 'io.jsonwebtoken:jjwt-jackson:0.11.5'
```
  
<br><br>  
  
# 근데 왜 3가지임?  

- jjwt-api : 인터페이스  
- jjwt-impl : 구현체
- jjwt-jackson : json 변환 및 파싱을 위한 패키지
  
api 와 impl 로 인터페이스와 구현체를 패키지 수준으로 분리해뒀다.  
이렇게 패키지 수준에서 인터페이스를 분리하는데  
물론 버전 매칭이 중요해진다는 점이 단점이기는 한데 그만큼 강하게 인터페이스와 구현체를 분리한 방식이기도 하다.   
  
이렇게 패키지 레벨에서 아예 분리한 방식은 소프트웨어 아키텍처에 관한 고급 책에서 볼 수 있는 주장이다.  
  
> The book Practical Software Engineering: A Case-Study Approach  
> advocates for putting interfaces in seperate projects/packages.  
> [출처 - Should interfaces be placed in a separate package?](https://stackoverflow.com/questions/1004980/should-interfaces-be-placed-in-a-separate-package)  
  
이에 관해서는 심도깊은 내용이고 쟁점이 있어 보이니까 이정도만 알아두고 넘어가자.  
  
<br>
  
### Jackson 은 왜 따로 있는가 하면  
JSON 처리에 jackson 말고 GSON 같이 다른 처리 방식도 있어서 분리해둔 것이다.   
  
<br><br>  
  
# 근데 왜 runtimeOnly 임?  
  
일반적으로 java code에서 발생하는 의존은 컴파일 타임에서 필요하지만  
리플랙션이나 외부 플러그인 등으로 발생하는 구현체 주입은 런타임에서 의존성이 발생한다.   
따라서 이런 경우에는 runtimeOnly로 둔다.  
  
