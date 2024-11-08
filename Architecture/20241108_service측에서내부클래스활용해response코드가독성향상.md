# 가독성이 떨어지는 Response 맵핑 객체  

```java
public boolean existLineupDataInResponse(FixtureSingleResponse response) {
    return !response.getResponse().get(0).getLineups().isEmpty();
}
```

위에서 보듯 response 객체는 중첩된 JSON 구조를 가지고 있어서 맵핑 객체의 가독성이 떨어진다.  

따라서 가독성 문제를 해결하기 위해 내부 클래스를 활용할 수 있다.  

<br>  

# 내부 Values 클래스 활용 

```java
@Service
public class LineupService {

  ...

  @AllArgsConstructor
  private static class ResponseValues {
    private final long fixtureId;
    private final long homeTeamId;
    private final long awayTeamId;

    ResponseValues(FixtureSingleResponse response) {
      try{
        this.fixtureId = response.getResponse().get(0).getFixture().getId();
        this.homeTeamId = response.getResponse().get(0).getTeams().getHome().getId();
        this.awayTeamId = response.getResponse().get(0).getTeams().getAway().getId();
        ...
```

위와 같이 `@Service` 내부에 `private statics class` 를 두고 API 응답 객체를 `@Service` 내부에 둔다.  
이렇게 하면 

```java
Fixture fixture = fixtureRepository.findById(responseValues.fixtureId).orElseThrow();
Team home = teamRepository.findById(responseValues.homeTeamId).orElseThrow();
Team away = teamRepository.findById(responseValues.awayTeamId).orElseThrow();
```

이런 식으로 가독성을 향상시킬 수 있다.  

<br>  

# private `static` class 인 이유 - 메모리 누수 방지   

[static inner class 가비지 컬랙션에 관하여](https://github.com/PhysicksKim/TIL/blob/main/Java/20230519_StaticClass%EC%97%90%EA%B4%80%ED%95%B4%EC%84%9C%EC%98%A4%ED%95%B4%EC%99%80%EC%95%8C%EA%B2%8C%EB%90%9C%EA%B2%83.md#class%EC%97%90-%EB%8B%AC%EB%A6%B0-static%EC%9D%98-%EB%8F%99%EC%9E%91-%EB%B0%A9%EC%8B%9D)  
