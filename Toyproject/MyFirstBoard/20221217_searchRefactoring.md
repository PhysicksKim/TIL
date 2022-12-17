[앞선 리펙토링 - 20221206_pagination클래스가godClass여서Refactoring필요.md](MyFirstBoard/20221206_pagination클래스가godClass여서Refactoring필요.md)  


# 앞선 리펙토링 요약    
  
![image](https://user-images.githubusercontent.com/101965836/208234046-0e7271b1-aad8-4c35-b388-3574e7339571.png)   
앞선 리펙토링에서 위의 클래스 다이어그램과 같은 결과를 얻었다.  
  
각각 좌측부터,  
1. 클라이언트로부터 요청된 파람들을 매핑해서 받는 DTO 
2. 글 목록을 얻어오는 DAO 
3. ViewPage 하단에 페이지 버튼 표시를 위한 DTO   

이렇게 리팩토링 됐다.  
  
<br><br>  

# Search 관련 클래스와 로직도 리팩토링 필요   
[게시판 제작법 블로그의 글](https://forest71.tistory.com/19) 을 따라하다보니   
기존 Search 객체는 Page와 의존성을 띄고 있었다  
하지만 SearchKeyword 와 SearchType 을 따로 담도록 분리하고    
페이지 관련 정보는 앞선 리펙토링 결과로 얻은 클래스들을 활용하는편이 좋지 않을까?  

<br>  

# 페이징 + 검색 (하나로 해결)  VS  페이징 / 검색 (분리)

## 1. 페이징 + 검색 (하나로 해결)  
하나로 해결하면, 그냥 심플해진다는 장점이 있다.  
하지만 현 상황에서는 DTO DAO DTO 3개나 있는데  
여기에 또 각기 상속하고 search 만들면  
오히려 또 String searchKeyword , SearchType searchType 같은 중복이 생긴다.  
  
### 중복 예시 코드  
```java
public class SearchRequestDTO extends PageRequestDTO{

    private SearchType searchType;
    private String searchKeyword;

}
```
```java
public class SearchDAO extends PageDAO {

    private String searchKeyword;
    private SearchType searchType;

    ...
}
```
이렇게 searchKeyword, searchType 중복이 생긴다  

### 그러면 위임을 통해서 Search 를 따로 빼도 되지 않을까?  

```java
public class Search {

    private SearchType searchType;
    private String searchKeyword;
    
}
```
```java
public class SearchRequestDTO extends PageRequestDTO{

    private Search search;
}
```
이렇게 해도 되긴 될것같다.  
하지만 궁극적으로는 아래와 같은 문제가 있다  
  
### 문제점 : 그래서 이걸 왜 하나에 다 놔둘려고 하는건데?  
1. 클래스 하나에 "페이징 + 검색" 다 넣기  
```java
List<Post> searchList = boardService.getSearchPostList(searchDAO);
```

2. 클래스를 나눠서 "페이징" / "검색"  
```java
List<Post> searchList = boardService.getSearchPostList(searchDAO, PageDAO);
```

자. 위의 1과 2중 뭐가 더 깔끔한가?  
솔직히 별로 차이 없지않은가?
  
오히려 개인적인 생각은, 역으로 2 방식이 더 가독성이 좋을 수 있다.  
  
다른 개발자가 2 방식을 보면  
> 아 검색결과 얻어올려면, 페이징 관련 정보랑, 검색 정보가 같이 넘어가야 하는구나  
라고 생각할 수 있지만  
  
1 방식을 보고 페이징과 검색정보가 같이 넘어가는지 아닌지 알려면  
searchDAO 구조까지 알아야 하는데  
또 상속으로 복잡하게 PageDAO 까지 알아야 하는 지경에 이른다.  
  
그럴꺼면 그냥 분리해두는게 더 심플하지 않겠는가?  
  
---
  
## 결론 :  페이징 / 검색 (분리) 가 나은 것 같다.  
