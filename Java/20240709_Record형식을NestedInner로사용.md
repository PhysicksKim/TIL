# 기존 Class 방식 - Nested Inner Static 
기존에는 API Request/Response 맵핑용 DTO 객체를 선언할 때 아래와 같이 사용했다.  

### 예시   
  
```java
@Getter
@Setter
@ToString
@JsonIgnoreProperties(ignoreUnknown = true)
public class FixtureResponse extends ApiFootballResponse {

  private List<_Response> response;

  @Getter
  @Setter
  @ToString
  @JsonIgnoreProperties(ignoreUnknown = true)
  public static class _Response {
    private _Fixture fixture;
    private _League league;
    private _Teams teams;
    private _Goals goals;
    private _Score score;
  }


  @Getter
  @Setter
  @ToString
  @JsonIgnoreProperties(ignoreUnknown = true)
  public static class _Fixture {
    private Long id;
    private String referee;
    private String timezone;
    private String date;
    private Long timestamp;
    private _Periods periods;
    private _Venue venue;
    private _Status status;
  }
```
  
특징으로   
   
- Class 는 응답별로 1대1 대응 되도록 한다
- 내부 클래스들은 이름 앞에 _를 붙여서 Inner Class 임을 명시한다
- Inner Class 는 다른 class 에서 사용되지 않고 해당 파일(Outer 클래스) 내부에서만 사용된다
- Inner Class 는 Static 을 붙여서 메모리 누수를 막는다. [이유](etc/20230213_DTO를innerStaticClass로만드는이유.md)    
  
<br><br>

# Record 를 API DTO 로 사용
  
Class 의 경우 섬세하게 조절이 가능하지만 Boilerplater 코드가 길어진다.  
Boilerplate 를 줄이기 위해 Lombok 의 @Annotation 들을 활용하지만, 이 또한 반복이 많다.  
  
그래서 이 모든걸 단순히 줄이고자 하여 Record 를 사용해보기로 했다.  
  
<br>  

```java
public record FixtureInfoResponse(
        long fixtureId,
        String referee,
        String date,
        String liveStatus,
        long leagueId,
        String leagueName,
        String homeTeamName,
        String awayTeamName,
        List<_FixtureEventResponse> events
) {

    public record _FixtureEventResponse(
            long elapsed,
            long teamId,
            _Player player,
            _Player assist,
            String type,
            String detail
    ) {
    }
    
    public record _Player(
            long id,
            String name,
            String koreanName,
            String photo
    ) {
    }

...

}
```

record 의 경우에도  
1. Inner Record
2. _ 스타일 네이밍 컨벤션
이 가능하다.  
  
jackson object mapper 에서도 이를 지원하며  
앞선 Inner Nested Class 를 사용한 맵핑용 DTO 와 동일하게 동작한다.  

> ## _ 스타일 네이밍 컨벤션
> DTO용 객체의 경우 JSON 생성을 위해 내부 클래스 구조를 많이 만들곤 한다
> 이때 Player, Team 같은 내부 클래스가 많이 만들어 지는데
> Entity 이름과 겹치기 쉬워서 네임 스페이스가 오염되는 문제가 있다.
> 이를 해결하기 위하여 "이 클래스는 Inner Class 입니다" 를 표시하기 위하여   
> 클래스 앞에 _ 언더바를 붙여서 내부 클래스임을 표시하도록 했다
> 이렇게 하면 DTO 클래스의 Nested 구조에서 자연스러운 이름을 사용하면서도 네임스페이스 오염을 막을 수 있다.    
    
<br><br>  
  
# Nested Record 의 Static 은 암시적으로 적용됨  
  
### Nested Inner Class 의 경우 Static   
을 사용하지 않으면  
Outer Instance 와 Inner Instance 간의 상호 참조가 생겨서 메모리 누수 문제가 발생할 수 있다.  
따라서 이를 막기 위해 Inner Class 의 경우 static 으로 선언하라고 IDE(Intellij)가 경고를 띄우기도 한다.  
  
### Record 는 static 을 암시적으로 추가해줌  
Record 의 경우 Inner Record 에 Static 을 달아주지 않아도 암시적(Implicitly)으로 static 을 추가해준다  
그래서 static 선언의 반복 조차 줄일 수 있다.  
  
