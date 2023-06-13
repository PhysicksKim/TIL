# 흐름  
1. Post Entity의 정적 팩토리 메서드를 만들었다  
2. 그런데 MapStruct 가 정적 팩토리 메서드를 인식하도록 해야한다.  
3. 그럴려면 기존에 Interface 형태로 Mapper를 제시해주던걸 Abstract Class로 바꿔야한다.  
5. 개발자가 일일이 Mapper를 어떻게 가져와야 하는지 알아야하는 문제가 있다. - MapStruct Running 문제    
6. 그래서 Mapper를 spring bean으로 등록해주고 bean 주입 받아서 사용한다.  
    
# 계기 : Post 의 생성자 은닉시킴  
자주 들어온 말  
  
> ### 정적 팩토리 메서드 사용을 고려하라  
  
이게 생각나서   
Post 의 생성자들은 다 은닉시키고  
  
```java
static public Post of(String title, String author, String content) {
    return new Post(title, author, content);
}
```
  
이렇게 정적 팩토리 메서드로 대체했다.  
 
### 문제 발생  
MapStruct는 Post 의 생성자를 인식해서 Entity - Dto 변환을 한다.  
그런데 이게 팩토리 메서드로 바뀌면 MapStruct가 인식하지 못한다  
  
그래서 
MapStruct 가 정적 팩토리 메서드를 인식해서 사용할 수 있도록 바꿔야 하는데  
Interface 방식으로는 이게 안되고, Abstract Class 를 사용해야 한다.   
  
> Interface 로 팩토리 메서드를 쓸 수 있을 것 같은데  
> 정확히는 잘 모르겠다.   
> 그치만 본질적으로 Interface 는 선언으로만 깔끔하게 가져가는 게 맞고  
> 구체적인 메서드를 많이 제시해야하면 Abstract Class를 사용하는 게 맞는 것 같다.  
  
<br><br>
  
# Mapper 변경 : Interface → Abstract Class
  
MapStruct 는 라이브러리가 자동으로 구현체를 만들어준다.  
이때 라이브러리에게 설계도를 제시해주는 방법으로는 2가지가 있다. 

> 1) Interface   
> 2) Abstract Class    
  
두 가지 방법의 각각 장점이 있다    
Interface 는 간단히 메서드 이름이나 어노테이션만으로 어떻게 구현되어야 할지 제시할 수 있다.  
Abstract Class 는 구현된 메서드를 넣어줄 수 있으므로 더 세세하게 동작을 정의할 수 있다.  
    
<br><br>  
  
# Mapper 어떻게 가져와야 하는지 알아야 하는 문제  
MapStruct 쓰는 건 좋은데  
문제는 다른 개발자 입장에서 어떻게 Mapper를 가져오는지 알아야 한다는 문제가 있다.  
물론 그다지 어렵진 않지만  

```java
CarDto carDto = CarMapper.INSTANCE.carToCarDto(car);
```

이렇게 한 줄의 코드일지라도 Mapper.ISTANCE.어쩌고저쩌고 를 일일이 쳐야한다는 게  
생각보다 귀찮음이 느껴졌다.  
  
그래서 @Bean 으로 등록하려 했는데 생각보다 간단하다  

<br><br>  

# 라이브러리가 Bean 등록 방법 제공 - @Mapper(componentModel = "spring")
```java
@Mapper(componentModel = "spring")
public abstract class BoardPostListDtoMapper {
```
  
이렇게 해주면 spring Bean 으로 Mapper를 그냥 등록해준다.  
  
  
<br><br>

## 직접 사용한 코드  

### Abstract Class Mapper

```java
@Mapper(componentModel = "spring")
public abstract class BoardPostListDtoMapper {

    @Mapping(
            target = "createdTime",
            expression = "java(formatCreatedTime(post.getCreatedTime()))"
    )
    public abstract BoardPostListDto toDto(Post post);

    // public abstract Post toEntity(BoardPostListDto boardPostListDto);

    public String formatCreatedTime(LocalDateTime createdTime) {
        LocalDateTime now = LocalDateTime.now();

        // 날짜가 동일한 경우 시:분 으로 표기 (14:52)
        if (now.toLocalDate().equals(createdTime.toLocalDate())) {
            DateTimeFormatter formatter = DateTimeFormatter.ofPattern("HH:mm");
            return createdTime.format(formatter);
        } else { // 날짜가 다른 경우 월/일 로 표기 (04/23)
            DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM/dd");
            return createdTime.format(formatter);
        }
    }

    @ObjectFactory
    public Post createPost(BoardPostListDto dto) {
        return Post.of(dto.getTitle(), dto.getAuthor(), ""); // Adjust this as per your needs
    }
}
```

### 사용 예시 - BoardService

```java
@Service
@Slf4j
@RequiredArgsConstructor
public class BoardService {

    private final PostRepository postRepository;

    private final BoardPostListDtoMapper boardPostListDtoMapper;

  ...
  
  public List<BoardPostListDto> getBoardPostList(Pageable pageable) {
    List<BoardPostListDto> result = new ArrayList<>();
    Page<Post> posts = postRepository.findAllByOrderByIdDesc(pageable);
    for (Post post : posts) {
        BoardPostListDto dto = boardPostListDtoMapper.toDto(post); // 이렇게 그냥 bean 주입 받은걸로 바로 사용가능  
        result.add(dto);
    }
    return result;
  }
  
  ...
}
```
