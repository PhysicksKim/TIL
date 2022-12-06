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
  
