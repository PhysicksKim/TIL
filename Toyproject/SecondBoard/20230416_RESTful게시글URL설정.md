# RESTful API 란  

URL Naming Convention 을 말한다  
  
### REST 명명 규칙  
    
1. 동사는 사용하지 않는다.  
2. URL은 명사로 이루어진 리소스로 구성한다.  
3. 하이픈(-)을 사용하여 단어를 연결한다.  

<br>

> REST API 라고 해서 꼭 API에서만 REST 방식을 써야하는 것은 아니다    
> 웹 URL도 RESTful URL Naming Convention 을 지키는 게 좋다  
> 어차피 RESTful의 목적이 유지보수성 향상에 있기 때문이다  

<br><br>  

# RESTful URL 예시  

### 1. 글 목록 조회

<code>GET /board/posts</code>

### 2. 글 상세 조회

<code>GET /board/posts/{postId}</code>

### 3. 글 작성

<code>POST /board/posts</code>

### 4. 글 수정

<code>PUT /board/posts/{postId}</code>

### 5. 글 삭제

<code>DELETE /board/posts/{postId}</code>

위 URL naming convention을 사용하면, HTTP Method와 URL을 통해 각각의 작업이 무엇을 수행하는지를 명확하게 알 수 있습니다.   
또한, 리소스에 대한 CRUD(Create, Read, Update, Delete) 작업을 모두 RESTful API로 구현할 수 있습니다.  
  
<br><br>
  
# 유의 : 가능하면 Path Parameter를 쓰자  
  
### 잘못 된 예시  
  
- 글 조회  
<code>GET /board/view-post?id={postId}</code>  
  
어떤 게시글을 보는지를 Query Parameter로 구분한다.  
  
<br>  
  
### 문제점  

1. URL Path Parameter는 직관적입니다.  
URL 자체에 해당 데이터의 정보를 포함하고 있어서 이해가 더 쉽다  
예를 들어 /post/123이라는 URL은 123번 글을 보고 있다는 것을 바로 알 수 있다.  
  
2. URL Path Parameter는 검색 엔진 최적화에 유리합니다.
Query Parameter를 사용하면 검색 엔진이 해당 URL을 분석하는 데 어려움이 있다.  
반면, URL Path Parameter를 사용하면 검색 엔진이 URL을 분석하고 색인화하는 데 더 유리하다.  
  
3. URL Path Parameter는 변경에 강합니다.
Query Parameter를 사용하면 하나의 쿼리 파라미터가 변경되면,   
캐시를 지우거나 새로 고침하는 등의 작업이 필요하다.   
반면, URL Path Parameter를 사용하면 새로운 URL을 만들어내기 때문에 변경에   
브라우저가 변경을 쉽게 인식할 수 있다.  
  
<br>  
  
### 좋은 예시  
  
<code>GET /board/posts/{postId}</code>  
  

<br><br>  

# 확장 - HTML 에서도 HTTP method 사용  

HTML form 에서는 GET, POST 만 사용 가능하고 PUT, PATCH, DELETE 등은 사용할 수 없다  
  
하지만 ajax를 통해서 HTTP 메서드를 쓴다거나  
Spring 과 Thymeleaf를 사용해서 HTML form에 Hidden으로 메서드 정보를 추가로 담는 등  
REST스럽게 URL을 설계할 수 있도록 다양한 Method 활용 방안을 제공한다  
  
따라서 이런 부분들을 적극 활용하면 웹에서도 REST설계를  유지보수성을 증진시킬 수 있다  
