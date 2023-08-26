# Transactional 

말 그대로 DB Transaction 을 걸어주는 어노테이션이다.    
method 에 @Transactional 걸어주면, 해당 method만 트랜잭션 처리가 된다.  
class 에 @Transactional 걸어주면, 해당 class 의 모든 메서드에 트랜잭션 처리가 된다.  
  
<br><br>  

# @Transactional 을 @Service vs @Repository 어디에 달아야하나?  

### 선 요약  
보통은 @Service 에 @Transactional 달아준다.  

### 왜 Transaction 쓰는지 다시 생각해보자 
해당 메서드가 실행되는 동안 데이터들이 딱 일관되어야 하는 경우 Transaction을 쓴다.  
  
읽기 + 쓰기, 읽기 + 읽기 (두 번 이상 읽는 경우), 쓰기 + 쓰기 등    
DB에 쿼리가 여러 번 날아가야 할 때 Transaction 을 써야한다.    
  
> ### 왜 쿼리가 여러 번 날아갈 때 Transaction?
> 쿼리가 여러 번 날아가는 동안 데이터가 바뀔 수 있기 때문이다.
>    
> 10개의 쿼리로 값을 읽어서 보고서처럼 데이터 조회한다고 해보자.
> 그런데 10개 쿼리가 진행되는 동안 중간에 값이 바뀌면 보고서에 값 불일치가 나타날 수 있다.
>   
> 예를 들어
> 회원의 남은 돈과 주문한 상품의 수가 있다고 해보자.  
> 이때 쿼리 진행되는 동안 회원이 1,000,000원 어치 상품을 주문할 수 있다.  
> 그런데 돈은 줄었고 주문한 상품의 수는 변함 없는 값으로 되어있다면 보고서에 문제가 생긴다.
> 따라서 읽기에도 특정 시점의 값이 일치되어야 하는 경우 Transaction 이 필요하다
  
  
### @Service 에 @Transactional 을 달아주는 이유    
  
```java
@Service
@Transactional
@RequiredArgsConstructor
public class PostService {

  private final PostRepository postRepository;

  public List<Post> getPostList() {
    // ...
  }
}
```

```java
@Repository
public class PostRepository {
  // ...
}
```

@Repository 의 메서드는 대체로 쿼리 하나에 대응된다    
반면 @Service 의 경우 로직과 얽혀서 여러 쿼리가 같이 섞인다.  
따라서 여러 쿼리가 같이 들어가는 @Service Class 에다가 @Transactional 을 달아주는 게 좋다.  
  
@Controller 입장에서는 
@Transactional 이 필요한 경우에는 @Service를 사용하고    
@Transactional 이 필요 없는 경우 @Repository를 사용하면 된다.  
    
