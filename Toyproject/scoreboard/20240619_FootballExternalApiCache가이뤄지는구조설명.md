# \[ Web 묶음 \] - \[ Domain 묶음 \]

Spring MVC 및 Layered Architecture 를 처음 배울 때에는 아래와 같이 단순히 배운다  
  
```
Controller - Service - Repository
```
  
이 구조로도 충분히 내 프로젝트 정도는 해낼 수 있다.  
그치만 단일 Service 만으로는 단점이 있다.  
  
- Service 하나가 너무 많은 역할을 지님
- 역할이 많아지므로 단위 테스트 어려움
  
따라서 여러 개의 Service 로 나누는 게 좋아보인다  
  
<br><br>  
  
# Web Service / Domain Service 
Web Service 는 Request/Response 처리를 담당하고  
Domain Service 는 도메인 로직/비즈니스 로직을 담당한다.  
  
그리고 Web Service 가 Domain Root 을 사용하여 요청을 처리하도록 한다.  
  
이러한 구조는 { Web 관련 처리 } / { 비즈니스 로직 처리 } 로 나눌 수 있다는 점에서   
다른 개발자들도 명확히 구조를 이해할 수 있도록 해준다.  
  
<br><br>  
  
```
├── ScoreBoardApplication.java
├── domain
├── web
```

위와 같이 domain 과 web 으로 디렉토리를 나누고  

```
web
 └── football
     ├── controller
     ├── dto
     └── service
```
```
domain
  └── football
      ├── ...
      ├── service
      └── FootballRoot.java
```

이와 같이 web 과 domain 을 나눠서 service 를 위치시키면 된다.  
  
<br><br> 

### Request 가 처리되는 순서  

```
(web 책임 담당) [Controller - WebService] ---(domain으로 연결되는 경계)--- [ DomainRoot - DomainServices - Repository ]
```

이와 같이 Web 과 Domain 을 잘 분리해준다면  
명확하게 각 계층이 어떤 역할을 하는 지 알 수 있게 된다.  
  
