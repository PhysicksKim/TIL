> 원래는 Pagination 이라는 이름으로 클래스를 만들었던걸   
> 리펙토링 도중에 Page 라고 클래스 이름을 바꿨다.  
> 그래서 클래스는 Page고 객체이름은 pagination 이고 해서 혼란스러울 수 있는데  
> 둘 다 다른게 아니라 같은거고, 객체 이름만 과거 pagination으로 해뒀던 흔적이 그대로 있는것이니, 혼동하지 않길 바란다.  
> 설명 중에 Page 클래스, Page 객체, pagination 객체가 혼용될 수 있다. 엄밀하게 쓰지 않고 그냥 다 같은놈들이고  
> 클래스와 객체의 의미는 좀 분별해서 쓸려고 노력했다.   

  
# Pagination 클래스  
페이징 관련 DTO 로 쓸려고 만들어둔 Pagination 클래스가 GodClass가 된 것을 발견했다.  

God Class란   
여기서도 쓰고 저기서도 쓰고   
너도 쓰고 나도 쓰고   
DB도 쓰고 view도 쓰고 controller도 쓰고 DTO로도 쓰고  
하는 아주 여러 계층에서 하나로 같이 퉁쳐서 쓰는 녀석을 말한다.  
     
MVC모델로 계층 분리해둔 이유가 의존성을 띄지 않도록 하기 위함인데   
이런 GodClass 가 있으면 당연히 의존성 문제가 생길 수 있다.  
그래서 꼭 따로 떼어주는 편이 좋다.  
(아니 근데 어쩌면 코드중복이 있을 수 있는데, 그런 경우에는 클래스 상속이나 인터페이스를 통한 위임을 활용해보도록 하자)  
  
<br><br>
  
# 문제가 되는 코드들 - 어떻게 의존성 문제가 생겼나?  

## 먼저, Page 클래스 소개 
<img width="577" alt="image" src="https://user-images.githubusercontent.com/101965836/205863953-d5fdb0cd-5cd3-4d8b-b843-930869ac5ac8.png">  
 
복잡할 거 없고, 그냥 대충 이런 필드가 있구나만 알면 된다.  
여기서 startPost나 pageSize 를 view 와 repository 에서 다 사용해서 문제가 생길 것이다.    

> 이후 볼 코드에서, 객체명들이 다 pagination 로 선언되어 있을거다. 헷갈릴 수 있으니 미리 다시 강조해둔다  
> 왜 이모양이 됐는지는 문서 최상단 참고  
> <img width="552" alt="image" src="https://user-images.githubusercontent.com/101965836/205865723-e79ce900-5b0d-452d-8ca4-1339615e5a5f.png">   
  
## 문제가 발생한 코드들 둘러보기  
  
### 0. controller 에서 받아온 param을 Page 객체가 받음  
<img width="578" alt="image" src="https://user-images.githubusercontent.com/101965836/205864748-f4b5cb53-7ac4-4a2d-9016-efaff4dfc697.png">  
<img width="612" alt="image" src="https://user-images.githubusercontent.com/101965836/205865234-8eff78ab-c473-4297-b7de-a20290899f59.png">  
  
이 코드를 짜던 당시에 @RequestParam을 한번 시험삼아 써보고 싶었다.   
그래서 "일단 @RequestParam 연습해보고 나중에 @ModelAttribute 로 바꿔야지~" 라고 생각하면서 짠 코드인데  
까먹고 그대로 @RequestParam으로 남겨놨다.  
어쨌건 DTO로 Page 클래스를 썼고, 그래도 Model에 담아버린 모습이다  
  
### 1. view 에서 pagination 사용  
<img width="700" alt="image" src="https://user-images.githubusercontent.com/101965836/205864351-4e2ecd21-f054-469a-861a-99ab7bd87118.png">  
<img width="777" alt="image" src="https://user-images.githubusercontent.com/101965836/205864408-abad0146-9b0b-4fd3-b0c9-5c2e5f133d63.png">  

```
th:classappend="${pagination.nowPage == 1} ? 'disabled'"
```
위와 같이 pagination 에서 nowPage 값을 갖고오는 모습을 볼 수 있다.  
역시나 view에서 pagination 객체를 갖다쓰니 의존이 발생했다.  
  
### 2. repository 에서 pagination 사용
<img width="646" alt="image" src="https://user-images.githubusercontent.com/101965836/205866846-e33e3ba1-8ea2-4ecd-a2ff-67e45b7511ae.png">  
DAO 를 분리하지 않고, 그대로 pagination 객체를 또 갖다가 쓰는 모습이다.  

<br><br>
  
# Refactoring 계획  

pagination이 어디에 쓰였는지 다시 정리해보자  

1. request -> controller DTO 에 쓰임  
2. controller -> (model) -> view 에 쓰임  
3. repository(DAO)에 쓰임  
  
그러면 이제 위 3가지 pagination 객체를 분리함이 맞다.  
  
<br>  

## 근데 코드 중복이 3개나 생기는데??
  
그대로 두면 결합이 너무 심해져서 나중에 유지보수가 끔찍해지겠고  
그렇다고 분리해두기에는 코드 중복이 발생한다.  
예를 들면 DTO에는 nowPage 라고 하고, DAO에는 page 라고 하고, view에서는 currentPage라고 해서 뒤죽박죽이 될 수 있다.  
셋 다 어차피 처음 request에서 들어온 다음에는 변하지 않는 똑같은 녀석인데 말이다.   
이건 어쩌면 인터페이스로 해결할 수 있을 것 같다.  
Pagination 이라는 인터페이스를 만들고  
각각 3가지로 나뉠 PageRequest, PageDTO, PageDAO 클래스가 Pagination 인터페이스를 구현하도록 해서  
필드명을 nowPage 로 통일하도록 강제할 수 있을 것 같다.  
  
이렇게 인터페이스를 따로 둬서 '위임' 형태를 사용하는 식으로 객체지향적 코딩에 대해 배웠던 것을 활용해 볼 수 있을 것 같다.  
  
<br>

# 1차 refactoring 구조  
  
![image](https://user-images.githubusercontent.com/101965836/206729730-5b82da70-782b-4542-b6f9-7f86dda3b08c.png)  
  
위와 같은 구조를 띄고  
특징은  
  
1. 모든 DTO DAO 는 Page 를 상속한다 (Page는 nowPage, pageSize 를 필드로 가짐)    
2. ViewDTO 가 nowPage, pageSize, prevPageList, nextPageList 를 모두 가지고 있다.    
  
PageList를 따로 분리해서 DTO를 또 빼주는게 나을까 싶었는데  
일단은 하나에 다 둬도 될 것 같아서 같이뒀다  
  
### 하나 고민되는 부분은  
PageList를 구하는 로직을   
DTO 클래스에 두기 vs Service에 두기  
인데  
  
내가 아는바로는 DTO는 추가 로직을 안둔다고 해서 Service에 로직을 두는게 나은가 싶었는데  
그러면 또 Service 하나가 메서드를 너무 많이 갖고 있어서 가독성에 좋지 않을까봐   
그냥 클래스에 뒀다  
  
---
### Page Class Family Diagram

![pageClassDiagram](https://user-images.githubusercontent.com/101965836/208119322-41c072e1-dd85-442c-b1a3-06425773825d.png)

위와 같이 Page 클래스가 부모 역할을 하면서 page, pageSize 를 상속해준다.  
모든 Page를 구현한 클래스에서 공통적으로 page와 pageSize를 사용하기 때문에 이렇게 해줬다.  
   
#### 생성자 공통적인 부분  
각 자식클래스들은 생성자 매개변수로 int page 와 int pageSize를 받고,    
처음에 super(page, pageSize) 를 해서 필드에 값을 넣어준다.    
   
#### PageDAO  
쿼리를 날릴려면 게시글 offset을 얼마나 할지 정해야 한다.  
(매번 말하지만, 오프셋 페이징이 나쁜걸 알고있고, 추후 커서페이징으로 바꿀 예정)   
현재 page와 pageSize를 바탕으로 얼마나 offset이 필요한지 생성자에서 간단히 계산해서 필드에 대입해준다.  
  
### PageViewDTO  
View에서 아래와 같은 페이지 버튼을 표시해주기 위해 pageList를 만들어서 전달해줘야 한다.

--- 
1️⃣ 2️⃣ 3️⃣ '4️⃣' 5️⃣ 6️⃣ 7️⃣ 8️⃣
---

현재 페이지가 4이고, static int pageButtons가 4일 경우  
prevPageList는 { 1 , 2 , 3 } 이 담겨야 하고  
nextPageList는 { 5 , 6 , 7 , 8 } 이 담겨야 한다.  
  
문제는 이걸 계산하는 로직이 좀 길어져서 따로 메서드로 빼줬는데  
인터넷에 검색해보면 DTO는 로직이 없이 값만 담고있는 메서드라고 해서  
"어? 그러면 boardService 에서 prev랑 next pageList를 계산하도록 바꿔야 하나?"  
라고 생각이 들었다.  
  
그런데 이러면 또 역으로  
boardService와 PageViewDTO의 의존관계가 아래와 같이 추가될거다  
  
![pageClassDiagram_boardServiceDepend](https://user-images.githubusercontent.com/101965836/208122183-89f886c6-a304-4820-8474-f0c12a47cf17.png)  
  
이렇게 되면 BoardService 없이는 PageViewDTO를 생성할 수 없게 된다.  
이때의 **문제점은 2가지** 정도 예상된다.  
  
#### 1. 다른 개발자가 PageViewDTO를 만드려 할 때  
다른 개발자가 PageViewDTO를 만들려면  
> "BoardService 에 있는 calculPrevPageList() calculNextPageList() 를 이용해서 pageList int\[]배열을 만든다음 값을 넣어줘야한다" 
라는 것을 알아야 한다.  
이는 적절히 추상화되지 않아서 부적절한 설계이다  
   
#### 2. PageViewDTO와 BoardService에 의존성이 생긴다  
BoardService에 있는 method를 이용해야지만 PageViewDTO를 만들 수 있다.    
상속같은 객체지향적 관계를 맺은건 아니지만   
다른 클래스의 특정 메서드에 의존적이므로 의존성이 생긴다고 말할 수 있다.  
  
또한 만약 prev/nextPageList 로직이 바뀌더라도,   
boardService에 있는 메서드를 바꾸는 것 보다는  
그냥 새로은 PageNewViewDTO 를 만드는게 깔끔하다.  
  
---
  
### 그래서 PageViewDTO 안에 로직을 다 넣어놨다  
nowPage와 pageSize만 넣어주면  
생성자에서 알아서 calculPrev/NextPageList() 해줘서 자동으로 넣어주는게  
딱 깔끔하게 추상화되기 때문이다.  
  
다른 개발자가 PageViewDTO를 만들려 하더라도  
> nowPage에는 현재 사용자가 보는 페이지 번호를, pageSize에는 페이지당 표시할 게시글 수를 넣으면 됩니다  
라고 간단한 한 문장으로 어떻게 만드는지 이해할 수 있게 된다.  
  
# 결론은  
부모 클래스가 될 Page를 만들고, 이를 상속해서 각종 DTO와 DAO를 만들었다.  
(Page를 abstract로 바꿔도 될 것 같다)  


