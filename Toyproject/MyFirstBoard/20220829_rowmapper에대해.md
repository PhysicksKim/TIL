# RowMapper란?  
```java
public List<Post> getPostList() {
  
  List<Post> postList = jdbcTemplate.query(sql, postRowMapper());

  ...
}


private RowMapper<Post> postRowMapper() {
  return BeanPropertyRowMapper.newInstance(Post.class);
}


```

이런식으로 쿼리 resultSet을 자료형에 맞춰 넣어주는 것이 RowMapper다  
예를 들어  
위의 Post에는 id title writer content date 같은 필드 변수들이 있다.  
만약 기본 jdbc에서 resultSet을 직접 써야 할 때에는  
일일이 setter를 이용해서 다 집어넣어줘야 했다.  
  
근데 rowmapper를 사용하면  
쿼리 결과값들을 row별로 post객체를 만들어 쏙쏙 넣어주고  
위의 경우 여러 post 들이 mapping되니까 List\<Post\>로 List에 담아서 반환해준다  
  
<br><br><br>  
  
# 근데 이걸 왜 직접 쓸려고? 

```java
public Post save(Post post) {

  String sql = "INSERT INTO BOARD(TITLE, WRITER, CONTENT) "
                + "VALUES(:title, :writer, :content)";
                
  ...

  jdbcTemplate.update(sql, sqlParam, keyHolder);

  Map<String, Object> keys = keyHolder.getKeys();
  // keys 에는 "ID" "DATE" 두 가지 있음
  // log.info(keys.get("ID").toString());    // 예 : 3
  // log.info(keys.get("DATE").toString());  // 예 : 2022-08-21 21:21:14.793822

  post.setId(Long.parseLong(keys.get("ID").toString()));

  // 2022-08-21 21:21:14.793822
  String sqlDateString = keys.get("DATE").toString();
  SimpleDateFormat formatter = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
  
  ...
  
}
```
  
Post 값을 집어 넣는건데   
post에서 글번호(id)나 작성시간(date)은 db에서 정하는 값이므로   
post를 return하기 위해서 id랑 date값을 채워넣기 위해서  
keyHolder로 쿼리결과를 받아와서 설정해서 넘긴 코드이다.  
  
근데 보면 키홀더로 받아온 값을 일일이 맵핑해서 넣어주고 있다  
 
이부분이 좀 너무 하드코딩 같아서  
어떻게 RowMapper를 이용해서 할 수 있지 않을까?  
  
<br><br><br>

# 방법 찾아 봤는데...
```java
BeanPropertyRowMapper.newInstance(Post.class);
```
이 부분을 위 코드에서 볼 수 있는데  
BeanPropertyRowMapper 이거가 딱   
Post 클래스 받아서 알아서 쇽쇽쇽 필드 변수랑 getter setter 보고 맵핑 해주는 걸로 이해하고 있다 (뇌피셜)  
  
살작 코드 뜯어보니까  
결국은 RowMapper\<\> 이거를 구현하는거고  
또 파고들어가면 resultSet을 갖고 노는 것 같다.  
  
그러면 아마 어디 메소드가 딱 있을텐데...  
그게 또 Post 클래스 전체에 대해서 다루는거니까  
딱 id나 date만 mapping 시켜주는게 있을려나?  
  
일단은 이런 경우 별도로 메소드 만들어서 맵핑시켜주는 게 맞는 것 같다.  
