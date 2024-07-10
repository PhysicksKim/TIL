# 문제 발생 - 여러 __ToMany 들을 FetchJoin 하여 발생  

```java
@Entity
public class Fixture {
    @Id
    private Long fixtureId;

    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "live_status_id")
    private LiveStatus liveStatus;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "league_id", nullable = false)
    private League league;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "home_team_id", nullable = false)
    private Team homeTeam;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "away_team_id", nullable = false)
    private Team awayTeam;

    @OneToMany(mappedBy = "fixture", fetch = FetchType.LAZY)
    private List<FixtureEvent> events;

    @OneToMany(mappedBy = "fixture", fetch = FetchType.LAZY)
    private List<StartLineup> lineups;
}
```

Fixture Entity 에는 위와 같이 2개의 <code>@OneToMany</code> 가 있다.  
Fixture Info 에 대한 API 응답을 만들기 위해서는 events 와 lineups 까지 같이 가져와야 했는데  
이를 위해서 한번에 모두 다 가져오기 위해서 아래와 같이 Fetch Join 을 작성했다.  

```java
@Query("SELECT f FROM Fixture f " +
        "JOIN FETCH f.league l " +
        "JOIN FETCH f.homeTeam ht " +
        "JOIN FETCH f.awayTeam at " +
        "JOIN FETCH f.liveStatus ls " +
        "LEFT JOIN FETCH f.events e " +
        "LEFT JOIN FETCH e.player p " +
        "LEFT JOIN FETCH e.team t " +
        "LEFT JOIN FETCH f.lineups lu " +
        "LEFT JOIN FETCH lu.startPlayers sp " +
        "LEFT JOIN FETCH sp.player pl " +
        "WHERE f.fixtureId = :fixtureId")
Optional<Fixture> findFixtureByIdWithDetails(long fixtureId);
```

### 문제 발생  
<code>findFixtureByIdWithDetails()</code> 에 대한 테스트 코드를 작성하고 실행해보니   
아래와 같은 예외가 발생했다.  
  
```
org.hibernate.loader.MultipleBagFetchException:
    cannot simultaneously fetch multiple bags:
    [com.example.domain.Fixture.events, com.example.domain.StartLineup.startPlayers]
```
  
<br><br>  

# 이유 
### 여러 ToMany 들을 하나의 Fetch Join 에서 수행했기 때문  
  
위의 fetch join 을 보면 2개의 ToMany (events, lineups) 에 대해서 fetch join 을 수행한 것을 알 수 있다.  
이 때문에 MultipleBagException 이 발생한거다  
  
> Bag 이란 "순서가 없고 중복을 허용하는" 자료구조를 말한다.
> Set 과 List 를 섞은 모습을 생각하면된다.
> Java 에서는 Bag 자료구조가 없으므로 그냥 List 를 사용한다.

## 왜? - 성능 문제 우려  
Join 이 줄줄이 이어지면 문제는 쿼리 응답의 row 가 너무 많아져 성능 우려가 있다.  
이는 JPQL 관점이 아니라 실제로 SQL 쿼리와 응답이 어떻게 이뤄질지 생각해보면 된다.  

### Fixture 응답  

|FixtureId|LeagueId|HomeTeamId|...|EventSequence|EventType|
|---|---|---|---|---|---|
|1|4|10|...|1|CARD|
|1|4|10|...|2|GOAL|
|1|4|10|...|3|SUB|
|1|4|10|...|4|VAR|

위와 같이 응답이 만들어 질텐데  
1개의 Fixture 에 대한 정보들이 Event 별로 불필요하게 반복되는 것을 알 수 있다(FixtureId, LeagueId, TeamId)  

이는 하나의 ToMany 관계 정도까지는 어느정도 문제가 없지만  
여러 개의 ToMany 에 대해서 Fetch Join 을 한다면  
N개*M개 의 Row 가 추가로 응답에 생성되니까  
쿼리 응답이 불필요하게 길어지게 된다.  

따라서 Hibernate 는 이러한 성능 문제를 예방하기 위하여 MultipleBagFetchException 을 발생시킨다.  
  
<br>  

# 해결방법 - ToMany Fetch는 별도 메서드로 분리  
    
ToMany 들은 의도적으로 별도의 메서드를 사용해 가져오도록 분리한다.  

```java
public Fixture getFixtureWithEager(long fixtureId) {
    Fixture fixture = fixtureRepository.findFixtureByIdWithBasicDetails(fixtureId)
            .orElseThrow(() -> new IllegalArgumentException("존재하지 않는 경기입니다."));

    List<FixtureEvent> events = fixtureEventRepository.findEventsByFixtureId(fixtureId);
    fixture.setEvents(events);

    List<StartLineup> lineups = startLineupRepository.findLineupsByFixtureId(fixtureId);
    fixture.setLineups(lineups);

    return fixture;
}
```

위와 같이 별도로 분리하여서 가져오도록 fetch join 을 나눠주도록 했다. 
