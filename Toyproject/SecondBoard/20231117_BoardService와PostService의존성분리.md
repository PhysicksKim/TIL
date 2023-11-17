# BoardService / PostService
처음에 생각없이 그냥 막 코드를 짰을때  
Controller가 BoardService 와 PostService 를 모두 의존하도록 짰다.  
  
이후 다시 리팩토링 하려고 보니 의존성 문제가 발생할 것 같았고,  
리팩토링은 단순히 service 분리가 아니라, 
  
- dto 의존성을 어디까지 둘건가?  
- post entity 관련 로직은 어디서 해결할 것인가?  
- 게시판 관련 복잡한 로직은 어디에 둘 것인가?    
  
위와 같은 생각을 토대로 분리했다.  
  
<br><br>  
  
# 1. Controller 는 가능하면 하나의 Service 만 들고 있는 게 깔끔해 보였다    
Controller 는 기본적으로 View 와 관련된 로직을 담당해야 한다.   
그런데 Controller 에서 여러 Service 를 가지고 있으니 문제가 된다.  
  
예를 들어 해당 타입(ex.전체공지) 게시글 작성 요청 권한이 있는지 판별하는 로직이 필요하다고 하자.    
이걸 검증하려면 memberService 를 사용해야 한다.   
  
먼저 controller 에서 memberService 로 권한 체크를 한다.      
이후 boardService 로 게시글을 작성한다.  

```java
@PostMapping("/write")
public String writePost(WriteDto dto) {
  if(!memberService.canWrite(dto)) {
    throw new IllegalArgumentException("글 작성 권한이 없습니다");
  }
  boardService.writePost(dto);

  return ...;
}
```
근데 이렇게 하면 "Controller는 Web과 관련된 로직들을 담당하고, 비즈니스 로직은 Service 로 위임해야 합니다" 라는 원칙에 어긋난다.      
"게시글 작성 권한을 확인하고 작성 요청을 해야 한다" 는 비즈니스 로직 이다.  
처음에는 "이정도는 간단한 로직이니까 Controller 에 둬도 되지 않을까?" 싶었다.  
근데 나중에 한참 지나서 다시 보니까  
"어디서 수정 권한을 확인하는거지?" 라고 찾아 해맸다.  
  
어떤 경우는 controller 에서 처리하고,  
어떤 경우는 service 에서 처리하고,  
어떤 경우는 Entity 자체에서 처리하도록  
각종 로직들이 파편화 되어 있었다.  
  
분명히 문제가 있다. "어디에 몰아넣지?" 라고 생각한 끝에 Service에 몰아 넣자고 결론 지었다.  
  
<br><br>  
  
# Service 분리 필요성    
Controller 의 모든 로직을 BoardService 에 넣었다.    
그런데 BoardService 에서 또 다시 문제가 생겼다.  
   
아직은 Post 를 BoardService 에서만 다루기 때문에  
따로 PostService 를 분리할 필요는 없다.  
하지만 여전히 복잡해지는 문제가 있었다.  
"어디까지가 게시판 관련 로직이고, 어디까지가 Post Entity 관련 로직이지?"  
  
Post 객체 자체를 BoardService 가 들고 있는것은 문제가 없다.  
그런데 BoardService 가   

1. 게시판 로직
2. Post Entity 관련 로직

둘 다 모두 포함하고 있다.  
  
이 자체는 문제가 없지만   
"뭐가 게시판 로직이고, 뭐가 Post Entity 관련 로직이지?"   
"Post 작성자 명 수정 부분을 바꿔야 하는데 이게 어디에 있지?"  
라는 문제가 생겼다.  

이와 더불어 차후에도 문제가 생길 수 있을 것 같다.   
여러 게시판이 생겨서 BoardService 가 나뉜다던가   
게시판 외에도 Post 를 사용해야 하는 경우가 생긴다면   
BoardService 에서 직접 Post 로직을 들고 있는건 문제가 생길 것 같았다.  
  
그래서 BoardService 가 "게시판 + Post Entity" 로직을 둘 다 들고있지 않도록 나눠야 겠다고 생각했다.  
  
<br><br><br>  

# Post Entity 에 단순 수정 메서드 두고 / Service 는 추가적인 로직을 포함  
    
## Entity 가 수정 메서드를 가지고 있도록 하기   
  
여기서 잘 생각해야 하는 게  
"Entity 본인 column 수정 관련 메서드만 들고 있기" 이다.   
  
내가 진짜 바보같은 코드를 짠 부분이 있는데  
"연관 Entity 수정 메서드"를 만든 것이다.  
  
```java
@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED) // JPA 엔티티 생성을 위해
@AllArgsConstructor(access = AccessLevel.PRIVATE) // 정적 팩토리를 위해
public class Post extends AuditBaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "author_id")
    private Author author;

    @Column(nullable = false)
    private String title;

    @Column(nullable = false, columnDefinition = "TEXT")
    private String content;

    private Post(String title, Author author, String content) {
        this.title = title;
        this.author = author;
        this.content = content;
    }

    /**
     * Setter 를 열어두면 무분별한 수정이 이뤄질 수 있으므로, update 메서드를 별도로 생성.
     */
    public void updateTitleAndContent(String title, String content) {
        this.title = title;
        this.content = content;
    }

    // 여기서 부터 이하 멍청한 코드
    public void updateAuthor(String name) {
        author.updateName(name);
    }

    public boolean isGuest() {
        return this.author.isGuest();
    }

    ...
```
멍청하게 author 수정 부분을 Post Entity 내부에 포함하고 있다.  
거의 반년전에 쓴 코드인데, 대체 뭔 생각으로 저 코드를 썼는지 모르겠다.  

당연히 저 부분은 Entity 에 두는 게 아니라, PostService 같은 곳에서 아래와 같이 수정하도록 해야한다.  
```java
post.getAuthor().updateName(name);  
```

왜냐하면, Service 에 로직을 몰아넣으면서, Entity 는 최소한으로 자신에 대한 메서드만 두도록 해야한다.  
"그냥 어차피 author 수정 자주 쓰이는데, post에 두면 getAuthor() 중복을 줄일 수 있는 거 아니야?" 라고 생각할 수 있다.   
> 아마 옛날에 그렇게 생각해서 저 코드를 적은걸까?    
  
하지만 잘 생각해보면 단점이 있다.   
이러면 author 수정 방식이 둘로 나뉘게 된다.  
author entity 를 가져와서 수정할 수도 있고,  
post entity method 를 사용해서 수정할 수도 있다.  
이는 로직이 둘로 나뉠 수 있다는 점에서 문제 발생 염려가 있다.  
따라서 딱 Entity 본인 관련 수정 메서드만 두도록 하자.

<br><br>  
   
## PostService 는 Author 를 수정할 수 있다.  
Service 는 결국 로직을 몰아넣는 거다.  
따라서 PostService 에서 Author 가져와서 author.updateName() 해서 author 의존성을 해결하는건 큰 문제가 되지 않을 것 같다.  
결국 의존성 가지가 늘어나면 유지보수에 문제가 생긴다는 점인데,  
service 가 어차피 의존성을 몰아서 가지고 있으니 문제가 없다.  
  
> ## 만약 Service 에 들어간 로직이 너무 비대해지면 어떻게 되나요?
> 이렇게 되면 DDD(Domain Driven Design) 로 이어진다.
> DDD 에서는 Service 는 비즈니스 로직을 위임하는 역할을 하고, 핵심적인 로직 구현 및 의존성들은 모두 Domain 안에서 해결하도록 한다고 설명한다.
> 더불어서 Domain 에는 입구 부분 하나(Root Domain)가 딱 있고, 도메인 외부(ex. Service)에서는 입구를 통해서 로직을 호출하도록 한다.
> 마치 앞서 본 BoardService 가 BoardController 의 모든 요청에 대응되도록 하는 것 처럼 말이다.  
  
<br><br><br>  

---

<br><br><br>  

# 리팩토링 결과  

기존에는 BoardService 와 PostService 가 서로 막 얽혀가지고   
BoardService 에서 Post Entity 메서드를 호출하기도 하고  
PostService 가 Post Entity 메서드를 호출하기도 하는 등  
복잡하게 의존성을 띄곤 했다.  
  
특히 앞서 본 Post Entity 가 post.updateAuthor() 같은 메서드를 들고 있다던가  
BoardService 가 받은 Dto를 그대로 그냥 PostService 에다가 똑같이 전달해서 dto 의존성을 Controller-BoardService-PostService 까지 넘겨버리는 등  
문제가 많았다.    
  
그래서 이를 해결하기 위해   
명확하게 BoardService 와 PostService 의 역할을 분리했다.  
  
- BoardService : 게시판 관련 로직 담당, dto 해체분해 해서 하위 service 에 값 전달 담당  
- PostService : 하위 세부 Service에 해당. POJO 에 따라서 메서드들은 dto에 의존하지 않고 JAVA 기본 객체를 사용하도록 함.
  
그리고 Entity 에서도  

- Entity 본인 관련 수정 메서드만 둔다. 연관관계 Entity 수정 메서드는 두지 않는다.

```java
// 수정 전 
post.updateAuthor(name);

// 수정 후
post.getAuthor().updateName(name);
```

이렇게 바꾸게 됐다.  
