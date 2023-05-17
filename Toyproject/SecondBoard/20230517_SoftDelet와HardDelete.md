# 그게 뭐지  

### Soft Delete / Hard Delete 
"게시글 삭제" "댓글 삭제" 라고 말하지만     
실제로 DB 에서 row를 Delete 하지는 않는다.   
나중에 관리자가 삭제된 게시글이나 댓글을 확인해야 할 수도 있고,   
중요한 데이터가 실수로 삭제될 수도 있으니까  
최대한 데이터를 남기는 식으로 동작하는 게 좋기 때문이다  
  
그래서 나온 삭제 방식이 Soft / Hard 2가지 방식이다  

<br><br>  

# 1. Soft Delete  
table 에 해당 데이터가 로직상 삭제됐는지를 flag로 나타낸다  
  
|id|title|author|content|createdTime|isDeleted|
|---|---|---|---|---|---|
|1|제목1|작성자A|Hi|20230517 13:01:10|false|
|2|제목2|작성자B|Hello|20230517 13:02:21|false|
|3|제목3|작성자C|안녕하세요|20230517 13:03:41|false|
  
위와 같이 게시글들이 있고, id=2 인 row를 삭제한다고 하면  
    
|id|title|author|content|createdTime|isDeleted|
|---|---|---|---|---|---|
|1|제목1|작성자A|Hi|20230517 13:01:10|false|
|2|제목2|작성자B|Hello|20230517 13:02:21|true|
|3|제목3|작성자C|안녕하세요|20230517 13:03:41|false|
  
이렇게 isDeleted Flag만 true로 바꿔주는 방식이다.  
  
# 2. Hard Delete  
말 자체는 직접 table 에서 row를 삭제하는 것을 뜻하지만  
실질적으로는 audit 테이블로 삭제할 데이터를 옮겨두는 것을 포함한다  
    
- Posts
|id|title|author|content|createdTime|
|---|---|---|---|---|---|
|1|제목1|작성자A|Hi|20230517 13:01:10|
|2|제목2|작성자B|Hello|20230517 13:02:21|
|3|제목3|작성자C|안녕하세요|20230517 13:03:41|

여기서 id=2 인 row를 삭제한다면  
  
- DeletedPosts
|id|title|author|content|createdTime|
|---|---|---|---|---|---|
|2|제목2|작성자B|Hello|20230517 13:02:21|
  
데이터를 다른 table로 옮긴다       
  
<br><br>  

---

<br><br>

# 두 방식 장단점  
[Soft Delete vs Hard Delete](https://abstraction.blog/2015/06/28/soft-vs-hard-delete)  
   
1. Ease of Setup 구현 용이성   
soft : isDeleted 컬럼만 true/false 바꿔주면 되서 쉽다   
hard : 별도 테이블을 만들어야하고, 데이터 이동시 누락이 생기면 안되므로 더 어렵다   
  
2. Debugging 디버깅  
soft : 동작이 단순해서 디버깅이 쉽다  
hard : auditing 을 이용하면 debugging을 할 수 있다   
  
3. Restoring data 데이터 복원   
soft : flag만 바꾼 거라서 복원이라고 할 것 자체가 없다   
hard : 복원이 더 복잡하긴 하다. (audit 테이블에서 다시 옮겨야 하니까)  
  
4. Querying for active data 개발자가 쿼리를 어떻게 날려야하는지    
soft : 개발자가 실수로 flag 체크를 까먹을 수 있다  
hard : flag를 고려할 필요가 없어서 더 간단하다.  
  
5. View Simplicity 단순히 테이블만 봐도 삭제된 데이터를 한눈에 알 수 있는가  
soft : flag에 따라 삭제 여부가 판단되므로, table을 유심히 봐야한다  
hard : 삭제 안 된 데이터들이 곧 table에 존재하는 데이터니까 WYSWYG 라서 더 심플하다.    
  
6. Performance of operation 테이블 조회 성능 문제  
Update 쿼리만 날리는 게, Delete + Insert 쿼리 보다 빠름  
  
7. Application Performance 속도와 저장공간     
soft : 매번 where flag 쿼리를 날려야함. flag 때문에 테이블이 늘어나고, 삭제된 데이터까지 테이블 하나에 다 있어서 테이블이 비대해짐.    
hard : 그냥 조회하면 됨. 삭제된 데이터는 이동되므로 더 간단함.  
  
8. Database features compatibility  
- Cascading 문제  
soft : 삭제시 연관된 다른 테이블 삭제를 위한 cascading이 지원되지 않는다  
hard : cascading을 통해서 연관된 다른 데이터들을 같이 삭제할 수 있다  
  
- Unique Index 사용시 문제  
soft : 삭제된 데이터와 동일한 Unique Index 값을 사용할 수 없음. 데이터 무결성이 깨지기 때문이다.    
hard : 데이터가 삭제되고 다른 테이블로 이동하므로, Unique Index를 사용해도 무결성을 지킬 수 있다  
예를 들어 email에 unique index가 지정된 경우, 삭제후 재가입을 할 수 없는 경우가 있다.  
  
<br><br>  
  
---

<br><br>  

# 결론 : 각각 장단점이 있다  
  
장단점을 고려해서 결정하자.  
어떤 테이블은 soft가 좋을 수 있고  
어떤 테이블은 hard가 좋을 수 있다.  
  
또한 처음 개발해보는 나같은 경우  
soft로 구현했다가  
나중에 hard로 바꿔보는 것도  
좋은 경험이 될 것 같다.  
  
<br><br>  

검색어 : 삭제 flag 게시글 삭제
  
