# Closure Table로 구현 시작  

일단 Closure Table을 사용해서 대댓글을 구현해보기로 결정했다.  
  
<br><br>  

# Closure Table 이란

CommentClosure 라고 테이블을 하나 더 만들고  
"X번 댓글은 Y번 댓글의 대댓글입니다"   
를 일일이 표시하는 방식이다  

## 예시

### Comment 테이블

| id | content |
|----|------|
| 1  | root comment |
| 2  | first reply |
| 3  | reply to first reply |

### Comment Closure 테이블:

| ancestor_id | descendant_id | depth |
|-------------|---------------|-------|
| 1           | 1             | 0     |
| 1           | 2             | 1     |
| 1           | 3             | 2     |
| 2           | 2             | 0     |
| 2           | 3             | 1     |
| 3           | 3             | 0     |

여기서 1 , 1 , 0 이나 2 , 2 , 0 같이 자기자신을 가리키는 row가 중요하다  

<br><br> 

### 1. 자기자신을 가리키는 행이 없다면  - X 경우

"특정 댓글을 가져오고, 그 특정 댓글의 자식들을 모두 가져와라" 

```sql
SELECT * FROM Comment
WHERE id = 1
UNION
SELECT * FROM Comment
WHERE id IN (
    SELECT descendant_id FROM CommentClosure
    WHERE ancestor_id = 1
);
```

<br>  


### 2. 자기자신을 가리키는 행이 있다면  - O 경우

"특정 댓글과 그 하위를 모두 가져와라"  

```sql
SELECT * FROM Comment
WHERE id IN (
    SELECT descendant_id FROM CommentClosure
    WHERE ancestor_id = 1
);
```
  
<br>  
  
이렇게 위와 같이 비교해보면, 확실히 자기자신을 가리키는 행이 있는 게 좋음을 알 수 있다.  
  
<br><br>  

# 간략하게 Diagram
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/16cc49ce-265a-453a-8588-710208790e11)
  
Post 1개는 N개의 comment를 달고있다.  
1개의 comment 아래에는 N개의 대댓글이 달릴 수 있다.  

> 추가로, 1개의 comment 위로 N개의 부모 댓글이 있을 수 있다.
> 이 또한 Closure Table에 정보가 담겨있다  


<br><br>  
# 쿼리 성능에 대한 논의  
    
### sub query 사용 vs JOIN 사용   
  
- sub query 사용 
  
```sql
SELECT * FROM Comment
WHERE id IN (
    SELECT descendant_id FROM CommentClosure
    WHERE ancestor_id = 1
);
```
  
- JOIN 사용  
  
```sql
SELECT cmt.id, cmt.text, clo.ancestor_id, clo.depth 
  FROM Comment AS cmt
  JOIN CommentClosure AS clo ON cmt.id = clo.descendant_id
  WHERE clo.ancestor_id = 1
  ORDER BY clo.depth, cmt.id;
```
  
단순히 모든 댓글 내용 조회에는 sub query를 사용하면 되고   
대댓글의 tree 구조를 살리려면 아래의 JOIN 쿼리를 사용하면 된다.  
  
근데 이는 단순히 용도의 문제가 아니라  
성능상의 문제도 고려해야 한다. 
  
<br><br>  
  
# 이외에 고려 할 부분  

### 대댓글의 페이징 어떻게 할건가?  
그냥 댓글만 있다면 게시판 페이징과 다를 바 없겠지만,  
대댓글의 경우 tree 구조를 띄고 있으므로  
tree 구조를 그대로 살리려면 어떻게 페이징을 할지 잘 생각해야 한다.  
  
또한 Comment 와 CommentClosure 를 같이 써야 하므로   
일반적인 형태의 커서 페이징과 조금 다르거나, 아예 커서 페이징이 힘들 수 있다.  
  
따라서 테이블 구조 차원에서 커서 페이징을 어떻게 구현할 수 있을지도 잘 생각해봐야한다.  


<br><br>  

# 데이터 만들어서 성능 테스트 해보기  


### Python 으로 MySQL에 데이터 집어넣어보자  

Comment 테이블과 CommentClosure 테이블이 서로 연관된 값을 가지고  
개발자의 복잡한 의도가 담긴 ancestor_id, descendant_id, depth 값이 딱 연결되어야 한다.  
그래서 단순히 SQL에서 제공하는 프로시저나 함수로는 테스트 데이터를 만들기 어렵다.  
따라서 Python 같은 언어를 통해서 MySQL 에다가 데이터를 집어넣는 식으로 해야한다.  
  
