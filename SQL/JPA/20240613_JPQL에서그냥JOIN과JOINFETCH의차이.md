# JOIN vs JOIN FETCH
```
@Query("SELECT t FROM Team t JOIN t.leagueTeams lt WHERE lt.league = :league")
```

```
@Query("SELECT t FROM Team t JOIN FETCH t.leagueTeams lt WHERE lt.league = :league")
```

<br><br>  

# 지연로딩(JOIN) vs 즉시로딩(JOIN FETCH)  
  
FETCH 유무에 따라서 지연/즉시 로딩 차이가 생긴다.  
FETCH 가 붙으면 즉시 로딩이다.  
  
FETCH 단어 뜻 그대로   
"쿼리 실행 시점에 곧바로 JOIN 대상 테이블의 값을 FETCH 해오겠다"  
라는 의미로 생각하면 된다.  
  
