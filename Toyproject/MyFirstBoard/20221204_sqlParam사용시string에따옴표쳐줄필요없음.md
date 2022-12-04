# SqlParam 이란

```java
@Override
public void delete(long postId) {
    String sql = "UPDATE BOARD SET DELETEFLAG=TRUE WHERE ID=:id";

    SqlParameterSource sqlParam = new MapSqlParameterSource()
            .addValue("id", postId);

    jdbcTemplate.update(sql, sqlParam);
}
```
이런식으로 SqlParameterSource 를 통한 사용법이 자주 쓰이는데  
이번에 검색 관련 코드를 짜다가   
제대로라면 2개의 글이 검색 결과로 나와야하는데  
계속 0개가 리턴이 되는 것이다.  
  
그렇다고 Exception이 터지는 것도 아니고... 뭔가 싶어서 열심히 찾았는데 좀 황당했다  
  
<br><br><br>  
  
# 범인은 '' 작은 따옴표  

일반적으로 쿼리문에서 숫자를 쓸때는 그냥 WHERE ID = 1; 같은 식으로 쓰면 된다  
  
하지만 문자열을 쓸 때는 WHERE TITLE LIKE CONCAT('%','텍스트','%'); 이렇게 '' 작은 따옴표로 감싸줘야한다.  
  
그래서 너무 당연하게   
<img width="830" alt="image" src="https://user-images.githubusercontent.com/101965836/205484963-c07afa10-b00e-4137-b204-7aa13b6a5567.png">  
이렇게   
```java
switch (searchType) {
  case TITLE:
      stringBuilder.append("TITLE LIKE CONCAT('%',':searchKeyword','%') ");
      break;
  ...
```
':searchKeyword' 라고 썼다.  
   
근데... 
    
### ':searchKeyword' 라고 쓰면 안되고, :searchKeyword 라고 써야한다!!  

설마 이게 범인일줄 모르고 1시간동안 삽질했다  
해당 sqlParam이 String이면 알아서 '' 작은따옴표를 추가해주는듯 하다.  
근데 ':searchKeyword' 라고 해놓아버리니까  
이게 sqlParam이 제대로 치환 안된것 같다.  
  
## 정확히는 sqlParam 처리 결과가 어떻게 된거나면  

searchKeyword = 테스트
start = 0
size = 5
일 때

아래의 쿼리문이 파라미터를 거치면
```
SELECT * FROM BOARD WHERE DELETEFLAG=FALSE AND
    TITLE LIKE CONCAT('%',':searchKeyword','%')
    ORDER BY ID DESC LIMIT :start, :size ;
```
    
':searchKeyword'는 파라미터 치환 대상이 아니라 그냥 string으로 인식되어서 
제대로 파라미터 치환이 안되고 아래처럼 된다.  
  
```
SELECT * FROM BOARD WHERE DELETEFLAG=FALSE AND
    TITLE LIKE CONCAT('%',':searchKeyword','%')
    ORDER BY ID DESC LIMIT 0, 5 ;
```
  
## 버그 범인 테스트  
그래서 앞서 말한대로 ':searchKeyword' 가 그대로 남는지 테스트를 해봤다  
  
일단 버그 나던 코드 유지를 위해 아래와 같이 해주고  
<img width="671" alt="image" src="https://user-images.githubusercontent.com/101965836/205485249-23b2b428-a050-49c3-bcf3-6964efa228b6.png">  
  
<img width="395" alt="image" src="https://user-images.githubusercontent.com/101965836/205485269-0d32ba22-2a83-43b7-86ae-9a23053192bf.png">  

이렇게 검색해줬다  
이러면 이제 searchKeyword가 '테스트' 니까  
제목에 '테스트'가 들어간 글 2개가 나와야 한다  
  
하지만 결과는...  
아래에서 보다시피 :searchKeyword 가 들어간 글이 뜬것을 볼 수 있다  
<img width="1316" alt="image" src="https://user-images.githubusercontent.com/101965836/205485334-9f61e84a-50ce-401c-8304-9f8782c111ff.png">  
  
범인은 :searchKeyword 였던 것이다!!  
  
## 고쳤다 버그  
  
<img width="830" alt="image" src="https://user-images.githubusercontent.com/101965836/205485421-d4a157fe-ca4a-4346-a073-cdc215b86031.png">  
<img width="1314" alt="image" src="https://user-images.githubusercontent.com/101965836/205485410-96722543-53bb-4f97-abc5-5beeb684e0ee.png">  
  
