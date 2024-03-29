거의 대부분 뇌피셜입니다
  
---  
  
# SRP에 대한 고려가 필요해진 현 상황  

간단히 글을 쓰고 삭제하고 검색하는 게시판을 생각해보자.  
글 쓸때, 글 수정할 때, 글 읽을 때, 게시판 목록 등등 다양하게 DB에서 값을 꺼내서 보여줘야하는 상황이 있다.  
그러면 이제 각 상황에 따라서 알맞은 클래스를 만들어서 써야하는데  
Data를 담아서 전달해주는 클래스라해서 DTO(Data Transfer Object)라고 한다.  
또 DB에서 Data를 꺼내올 때 사용하는 DAO(Data Access Object)를 구분해서 말하며,  
DB에서 값을 꺼낼 때 각 행에 대응하도록 만들어둔 클래스를 Domain(=Entity)라고 한다.  
  
근데 여기서 중요한것은  
DAO와 Domain(=Entity)는 좀 분명하지만  
DTO는 뭔가 확실하게 용법이나 디자인 방식을 알지 못하면 복잡하게 꼬이기 쉽상인 것 같다.  
지금 내 프로젝트 상태가 딱 그렇다.  
예를들어 게시판 첫 페이지에서 글 목록을 쫙 보여주기 위해 만든 DTO가 있다.  
이름은 Page 라고 붙였는데  
문제는 Page DTO는   
\1. 글 목록 2. 게시판 페이징   
이렇게 두 가지 역할을 갖고 있다.   
  
그러면 여기서 의문이 하나 생긴다  
  
### "글 목록" 과 "페이징"은 '서로 다른 역할'인가?  
  
객체지향 원칙중 하나인 SRP(단일 책임 원칙 ; Single Responsibility Principle)가 있다.  
클래스가 딱 하나의 역할(책임)만 해야 한다는 것이다.  
그럼. 글 목록 보여주기와 게시판 페이징은 다른 역할인가?  
  
<br><br>   
  
# 무조건 분리 vs 일단은 같이 두자
  
가장 쉽게 생각할 수 있는 방식은  
조금만 역할이 달라보이면 무조건 분리하기다.  
  
하지만 이렇게 하는 것도 문제가 있는데  
  
1. 너무 무의미하게 다양한 클래스를 생성한다.  
2. 오히려 서로간의 의존성을 만들 수 있다  
  
1은 나눌 필요 없는 경우까지 다 나눠버릴테니 무의미하게 클래스가 많이 생길것임을 예상할 수 있다.  ('나눌 필요' 는 무엇을 기준으로 나눌것인가에 대해 밑에서 다룬다)    
2는 클래스를 나눴지만 결국 사용자에게 표시될 때는 하나로 합쳐야 하는 경우에 의존성이 발생할 수 있을 것이다.  
예를들어 사용자가 글 목록과 페이징을 따로 보지는 않는다.  
게시판에 들어오면 당연히 게시물과 페이징버튼이 같이 제시될 것이다.  
따라서 클래스를 서로 분리했더라도 결국 글 목록이 가는곳에는 페이징이 같이 갈 것이다.  
이러면 매 번 게시판을 보여줄 때 마다 글 목록과 페이징을 따로 만들어서 Model에 넣어주는 작업을 반복하게 된다.  
이럴 필요가 있을까?  
그냥 하나의 클래스로 합쳐버리고, 한방에 딱 초기화 되도록 하면 간단한 것 아닌가?  
  
또한 게시판이 한 페이지에 글을 10개 보여주다가 20개 보여주기로 바뀌었다고 해보자.  
그러면 이런 상황에서는 분리 or 같이두기 뭐가 나을까?  
아니면, 이런 사소한 경우는 중요하지 않고, 더 큰 담론이 제시될만한 어떤 가치가 있을 까?  
  
<br><br>  
  
# 일단 지금 생각하는 '나눌 필요' 가 있는 상황  
  
지금 현재 내가 생각하는 나눌 필요가 있는 상황은 다음과 같다  

1. 필드별로 초기화 시점이 달라지는 경우.  [참고](https://umbum.dev/1206)  
이 필드는 대체 언제 초기화되고, 이때는 왜 또 null 인거야?? 라고 차후 유지보수과정에서 혼란이 생길 수 있다.  
수정할려고 했는데 이건 언제초기화되고 이 필드는 왜 또 중간에 값이 바뀌고... 하다보면 유지보수 비용이 증가하게 된다.  
따라서 딱! 한번만, 명확하게 언제 생성되고 어디에 쓰이는지를 구분하기 쉽도록 나눠줘야한다.  
  
2. 영향을 미치는 범위가 너무 커질때  
사용자 -> 컨트롤러 -> view (-> 사용자)    
맞는 표현인지는 모르겠지만  
이렇게 계층간에 값이 전달되는 상황이라고 해보자.  
예를들어 사용자가 글을 수정했고, 이걸 컨트롤러가 받아서 수정하고, 다시 사용자가 수정된 글을 본다고 해보자.  
  
잘 생각해보면.  
만약 수정 작업이 DB에서 에러 없이 정상적으로 처리됐다면  
이전에 사용자가 수정한 글 내용을 그대로 재활용해서 사용자에게 보여줘도 될 것 같다.  
뭔가 DB 쿼리도 덜 날려서 마치 'DB 부하를 줄이는 묘수' 처럼 보이기도 한다.  
  
하지만 이는 차후 유지 보수 과정에서 문제가 생길 수도 있다.  
예를 들어 게시판 글 보여줄 때, 수정시각을 표시해주는 기능이 생겼다고 해보자.  
그러면 게시글 수정에 성공한 후, 사용자가 수정 시각이 게시글에 표시된 모습을 보여주는 게 좋을 것이다.  
근데 이렇게 분리안하고 재활용하도록 만들어 놨다면   
나중에 수정 시각을 보여줄 때 완전히 다시 로직을 짜야할 수 있다.    
하지만 애초부터 {사용자 -> 컨트롤러} 부분과 {컨트롤러 -> view} 부분을 분리하고,  
글을 수정할 때와 읽을 때를 완전히 분리해둔다면  
앞서 말한 문제가 줄어들 것이다.  
  
<br><br>  
  
# 결국 왕도는 없다.    
어디서 문제가 발생할지 예상하는게 말이 쉽지    
미래에 어떤게 새롭게 추가될지 알턱이 없는데  
이를 어떻게 완벽히 예상하고 코드 구조를 짜겠는가.  
  
다만 이를 위해  
앞선 천재들이 만들어놓은 객체지향 원칙을 따르면  
그나마 좋은 설계에 가까워 질 수 있을 것이고,  
그 중 하나가 이번에 중점적으로 생각해본 SRP 인 것 같다.  
  
이번 고찰을 토대로 게시판쪽 리팩토링을 한번 해봐야겠다.  
