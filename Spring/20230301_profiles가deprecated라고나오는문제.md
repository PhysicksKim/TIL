[출처](https://stackoverflow.com/questions/72013986/profiles-property-is-deprecated-is-properties-yml-for-spring-kafka)  
  
### 결론만 봐도 됩니다.  
  
---
  
# 문제 : properties 설정할 때 spring.profiles 가 Deprecated 떴다. 이거 써도되나?  
![image](https://user-images.githubusercontent.com/101965836/221921274-575f07b9-7d6e-4633-9b13-14a7f2bd0649.png)  
이렇게 deprecated 됐다는 메세지가 떴고,  
대신 spring.config.activate.on-profiles 를 사용하라고 메세지가 뜬다   

> ![image](https://user-images.githubusercontent.com/101965836/221921706-228cf667-7b99-4846-a5f9-29e5cb67529a.png)  
> [이미지 출처](http://honeymon.io/tech/2021/01/16/spring-boot-config-data-migration.html)  
> 이렇게 대신 spring.config.activate.on-profiles 를 써라는 메세지가 나온다.  
  
### spring.profiles.activate 는 제대로 동작하는데...  
deprecated 라고 뜨긴 하지만  
제대로 동작한다.  
  
```
spring:
  profiles:
    activate: local 
```
이렇게 쓰면 spring을 실행했을 때 로그에서  

```
... : The following 1 profiles is active: "local"  
```

이라고 뜨면서 정상적으로 local 프로파일이 활성화 됐음을 볼 수 있다.  
  

### 하지만, spring.config.activate.on-profiles : local 은 동작하지 않네?  
```
spring:
  config:
    activate:
      on-profiles: local  
```
이렇게 하면 로그에서  

```
... : No activa profile set, falling back to 1 default profile: "default"  
```

이렇게   
"활성화된 프로파일이 없으니까 그냥 default로 실행한다"   
라고 로그를 띄운다   
  
<br><br>  

---

# 해결 : 사실 spring.profiles.activa는 deprecated 된 거 아님   
  
무슨 말인가 하니  
  
**deprecated 된건 사실, spring.profiles 까지만 쓰는 방식이 deprecated 됐다는 뜻임**   
  
아래와 같이  
```
spring.profiles : local
```
이렇게 쓰는 방식이 deprecated 됐다는 뜻이다.    
  
---  
  
위 방식 대신    
```
spring.profiles.active: local  

---

spring.config.activate.on-profile : local  
# local 에 맞는 설정들 쭈욱...
```
이렇게 쓰는 방식을 쓰도록 바뀐거임  
  
### 근데, IntelliJ가 멍청하게도 spring.profiles 만 보고 deprecated 에러를 뱉는거임  
  
```
# deprecated 된 방식  
spring.profiles: local

# 옳은 방식  
spring.profiles.active: local
```

이렇게 .active 를 쓰더라도  
하위 경로만보고 intellij가  
spring.~~profiles~~.active 라고 취소선을 그어버림  
  
<br><br>  

# 결론 : deprecated 된 거 아님  

spring.profiles.active: local 라고 쓰는 게 맞다    
spring.profiles: local 로 쓰는게 deprecated 된 방식이다.  

근데 intelliJ가 spring.profiles 까지만 보고 그냥 멋대로 취소선 그어버림  
  
### 올바른 방식은  
```
spring.profiles.active: local  

---

spring.config.activate.on-profile : local  
  # local 프로파일용 설정들  
```
이렇게 하는 게 올바른 방식이다  
  
