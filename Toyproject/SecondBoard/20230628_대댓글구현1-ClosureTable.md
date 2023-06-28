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
