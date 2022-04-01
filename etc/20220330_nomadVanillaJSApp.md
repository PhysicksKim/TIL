# EventListner 추가하는 방식 2가지
```HTML
...
  <div class = "hi">
    <h1> Hello </h1>
  </div>
```
```JavaScript
...
const title = document.querySelector(".hello h1");

function handleMouseClick() { 
  ... 
}

title.addEventListener("click", handleMouseClick); # 방법 1

title.onclick = handleMouseClick; # 방법 2
```

이벤트 리스너를 추가하는 방법을 위와 같이 
1. .addEventListener(...) 방식으로 매소드를 이용
2. .onclick = 방식으로 프로퍼티를 사용

이렇게 두 가지 방법이 있지만, 여기서 첫 번째 방법을 추천한다.


#### addEventListener 방법 추천 이유
```JavaScript
title.addEventListener("click", handleTitleClick1); # 방법 1
title.onclick = handleTitleClick2; # 방법 2

title.removeEventListener("click", handleTitleClick1);
// title.removeEventListener("click", handleTitleClick2); # 방법 2는 이렇게 삭제할 수 없음
title.onclick = undefined ; # 방법 2는 이렇게 삭제해야함
```

첫 번째 방법은 .removeEventListener() 매소드를 이용해서, 두 번째 방법은 위의 코드처럼 = undefined; 같은 방식으로 삭제해야 한다. 코드 가독성부터, 이벤트리스너 관리까지 첫 번째 방법이 더 좋다. 특히, 매소드를 이용하면 옵션을 줄 수 있는등의 선택지까지 있다. 삭제가 중요한 이유는, 이벤트 리스너가 계속 존재하면 메모리 누수 문제가 발생할 수 있기 때문이다.  
  
또한 첫 번째 방법으로는 여러 이벤트 핸들러를 추가할 수 있다는 장점이 있다.  
  
<details>
  <summary>테스트용 코드 app.js</summary>
  
    const title = document.querySelector(".hello h1");
    const btn = document.querySelector(".btn h1");

    function handleTitleClick1(){
        console.log("click 1111");
    }

    function handleTitleClick2(){
        console.log("click 222222222");
    }

    function handleTitleRemove(){
        title.removeEventListener("click", handleTitleClick1);
        // title.removeEventListener("click", handleTitleClick2);
        title.onclick = undefined ;
    }


    title.addEventListener("click", handleTitleClick1);
    title.onclick = handleTitleClick2;


    btn.addEventListener("click",handleTitleRemove);  
      
</details>  
  
<details>  
  <summary>테스트용 코드 index.html</summary>
  
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <link rel="stylesheet" href="style.css">
        <title>Momentum</title>
    </head>
    <body>
        <div class="hello">
            <h1>
                Grab me!
            </h1>
        </div>
        <div class="btn">
            <h1>
                BTN!
            </h1>
        </div>
        <script src="app.js"></script>
    </body>
    </html>
     
</details>  
  
  
# JS 코드 흐름
1. 엘리먼트를 찾아라.
2. event를 listen 해라
3. event에 반응해라

 ---
 
# 좋은 코드 통찰

### (1) 좋지 않은 코드

```JavaScript

fuction handleTitleClick(){
  if (h1.style.color === "blue"){
    h1.style.color = "tomato";
  } else {
    h1.style.color = "blue";
  }
}
```  
  
---

### (2) 좀 더 좋은 코드

```Javascript
// (2-1) app.js

fuction handleTitleClick(){
  if (h1.className === "clicked"){
    h1.className = "";
  } else {
    h1.className = "clicked";
  }
}
```

```CSS
// (2-2) style.css

h1 { 
  color: blue;
}

.active {
  color: tomato;
}
```  
  
개선된 점 : CSS 파일에서만 CSS style 형식을 관리하고, js 파일에서는 CSS Class Name만 변화시킨다.   
이렇게 하면 CSS Style을 .js와 .css 두 군데에서 이중으로 관리해서 생기는 혼란을 줄일 수 있다.  

한계 : className은 다중으로 넣을 수 있다는 이점이 있다. 예를 들어 "clicked redFont bold" 처럼 여러가지 class를 부여하고, 여러가지 CSS 효과를 한번에 받도록 할 수 있다. 그러나 위 방법으로 하면 다중className의 이점을 가질 수 없게 된다. 예를 들어 "redFont bold" 였다고 치면, ""로 바뀌어 버려서 clicked와 관련 없는 특성도 사라져버린다.

---

### (3) 더욱 더 좋은 코드

```Javascript
fuction handleTitleClick(){
  const clickedClass = "clicked";
  if (h1.classList.contains(clickedClass)){
    h1.classList.remove(clickedClass);
  } else {
    h1.classList.add(clickedClass);
  }
}
```  
  
개선된 점 : classList로 클래스 프로퍼티에서 문자열 ""을 받아오는게 아니라, 어떤 클래스가 담겨져있는지 판단하고 추가하거나 삭제한다. 앞선 (2)의 코드의 한계를 보완했다.

한계 : 코드의 가독성 측면에서 좋지 못하다. if문이 늘어지면서 이걸 왜 하고 어떤 작업이 진행되는지 이해하는데 시간이 좀 더 걸린다. 

---

### (4) 제일 좋은 코드

```Javascript
fuction handleTitleClick(){
  h1.classList.toggle("clicked");
}
```  

개선된 점 : 지리멸렬하게 6줄 늘어지던 코드였는데, 기본적으로 지원하는 classList.toggle() 매소드로 한방에 해결했다! 적절한 매소드를 써서 한방에 시원하게 해결하는 게 가독성도 좋고 때때로 성능면에서도 더 좋기도 하다.   
   
#### 다시 설명
 
\1. JS에서 css style을 변경할 수 있다. 하지만 css파일을 분리하고, css파일에서 class에 따라서 색상을 달리하는 등의 방식으로 style을 미리 정해두고, JS에서는 class 이름만 바꾸는 식으로 style을 변경하는 게 좋다. 왜냐하면 JS에서도 style를 바꾸고 CSS에서도 style을 바꾸고 하면 혼란스럽기 때문이다.      
       
1-1. 그리고 이 방식을 추천하는 이유는, toggle 기능이 있기 때문이다. className은 여러 개를 가질 수 있다. class="boldFont active fontColorBlue" 처럼 여러 클래스 이름을 동시에 가질 수 있고, 각각의 CSS 효과를 한꺼번에 받을 수 있다. 이 때, 만약 일일이 className을 가져와서 여기에 active가 있는지 확인하고, if문으로 또 바꿀지 말지 체크하고 등등 하면 정말 귀찮은 작업이 된다.  
  
하지만 (엘리먼트).classList.toggle("active") 이렇게 단 한줄로 active 클래스가 있으면 제거하고, 없으면 추가하고 하는 toggle 기능을 구현할 수 있다. 엄청 편리하다! 
   
\2. 반복적으로 나오는 부분은 변수로 지정해서 관리하는 게 좋다. 예를 들어 CSS에서 .active { ... } 라고 class 단위로 묶어서 style을 지정했다고 하자. 그러고 js에서는 if 뭐뭐뭐.className === "active" { ... } 이런식으로 해서 "active"라고 적힌 부분이 아주 많이 반복된다고 하자.   
  
이럴 경우 당연히 active라는 이름을 바꿔야 한다면, "active"라고 적어둔 부분을 일일이 바꿔줘야하고, "active" 라고 입력하다가 에러가 날 수 있다. 이렇게 === "active" 같은 방식으로 입력해넣은 것을 개발자가 써넣었다는 의미의 raw data 라고 하며, raw data가 반복적으로 나와서 유지보수와 실수가 발생할 수 있는 상황을 줄이는 게 좋다. 나중에 코드가 복잡해지고 에러가 나면 왜 에러가 발생했지 하고 한참을 헤매는데, 이때 고작 오타 하나 때문에 발생할 수 있다. 그러니 오타로 인한 문제가 발생할 수 있는 상황을 최소화 하는 게 좋다.  

---

# JS로 입력 조건 제한하기 vs HTML로 입력 조건 제한하기
최대 글자 수 같은 경우 JS 뿐만 아니라 HTML 속성으로도 제한할 수 있다.
## JS로 15자 제한
```js
function onLoginBtnClick(){
    const username = loginInput.value;
    if (username === ""){
        alert("Please write your name");
    } else if(username.length > 15){
        alert("your name is too long");
    } else {
        console.log(username);
    }
}
```
## HTML로 15자 제한
```html
<form id="login-form">
    <input 
        required
        maxlength="15"
        type="text" 
        placeholder="What is your name?">
    <button>Log In</button>
</form>
```
HTML로 하는 편이 간결하다. 코드 가독성도 더 좋고, 불필요한 if절이 늘어지는 것을 막을 수 있다.


---

# form Submit 페이지 이동 막기 
## 기본 동작을 막는 event.preventDefault()
```JS
const loginForm = document.querySelector("#login-form");
const loginInput = document.querySelector("#login-form input");

function onLoginSubmit(event){
    event.preventDefault();
    console.log(loginInput.value);
}

loginForm.addEventListener("submit",onLoginSubmit);
```
  
event.preventDefault();  
이 매소드를 집중해서 보자. 일단 EventListener에 함수를 던져주면, callback 호출 될 때 함수의 인자로 event에 대해서 자동으로 받아온다. 그 다음, .preventDefault()를 하면 이벤트 별로 자동으로 수행되는 행동들을 막아준다.  
예를 들어 form에서 submit 버튼을 누르면, 자동으로 페이지가 이동하게 된다. 하지만 .preventDefault()를 하면, **"submit 할 때 페이지가 이동한다"** 라는 기본 행동을 막게 된다. 따라서 페이지 자동 새로고침을 막을 수 있다.  
  
## 이렇게 하면 링크도 막을 수 있다
```html
<a href="~~~">Link</a>
```  
\<a>는 기본적으로 다른 페이지로 하이퍼링크 걸 목적으로 만들어진 태그다. 그런데 이런 태그들도 이벤트 리스너를 추가하고 event.preventDefault() 해주면 기본 동작인 링크로 페이지가 이동하는 것 까지 막을 수 있다.  



---
  
  
# localStorage 기능
```JS
localStorage.setItem("name","Kim");
localStorage.getItem("name");
localStorage.removeItem("name","kim");
```
![image](https://user-images.githubusercontent.com/101965836/160975965-5cacbbd5-b178-486f-9ada-cc55089cfb79.png)  
새로고침후에도 어떤 상태를 저장해둘려면 localStorage 기능을 이용하면된다. key와 value로 간단한 값을들 저장할 수 있다.   

---

# Intervals
```JS
setInterval( callback함수, 반복 시간);
```

반복적으로 일어나야하는 경우에 사용한다. 얘를 들어 5초마다 주식 시장을 갱신한다거나, 1초마다 어떤 api를 사용해야하는 등의 작업에 사용한다.  

---

# Timeouts


---

# this의 동작 원리는 대체 뭐지?

### 주의 : 결론은 "잘 모르겠다" 입니다

코딩을 하다가 객체의 매소드를 정의할 때 화살표 함수를 썼다. 근데 알고보니 화살표 함수의 this는 Window를 지칭했다...  
이 때문에 뇌정지가 와서 잠깐 this에 대해서 좀 알아보는 시간을 가졌다.  
  
일단 화살표 함수는  
1. 무조건 익명함수로만 사용할 수 있음
2. 메소드나 생성자 함수로 사용할 수 없음  
이라고 한다  
  
그럼 뭐때문에 화살표 함수와 this의 궁합이 이모양이 됐을까. 
  
## function 선언의 this
```JS
let someone = {
    name : 'kim',
    whoAmI : function(){
        console.log(this);
    }
}
someone.whoAmI();
```

## 화살표 함수의 this
```JS
let someone2 = {
    name : 'kim',
    whoAmI : ()=>{
        console.log(this);
    }
}
someone2.whoAmI();
```

화살표 함수를 썼다는 차이가 있을 뿐 동작은 같은 코드이다. 하지만 결과는?  
  
![image](https://user-images.githubusercontent.com/101965836/160980525-448ab203-e22d-45e4-9a31-113d3f6ff90f.png)  
이처럼 화살표 함수가 지칭하는 this는 전역(Window) 객체가 된다.  
  
JavaScript에서 this는 다른 언어와 좀 다르게 동작한다. **대부분의 경우** (예외도 있다는 말이다) this가 가리키는 대상은, 호출을 **누가** 했냐에 따라 달라진다 라고 한다.  [공식문서](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/this)  
   
사실 이 내용은 30분 이상 강의를 들어야 하는 복잡한 내용인 것 같다. 지금 3시간 동안 this만 찾은 나도... 아직 this가 뭘 가리키는지 딱 말해봐라 하면 100% 정답은 힘들 것 같다.  
그래도 [이 강의](https://www.youtube.com/watch?v=GteV4zfqPIk) 가 조금 도움됐고, 좀 더 알아보고 시행착오를 겪은 뒤에 정리가 되면 글로 남겨야 겠다.   


---

# 추가 삭제 

## 추가
```js
const toDoList = document.getElementById("todo-list");

function addToDo(newToDo){
  const liToDo = document.createElement("li");

  const spanToDo = document.createElement("span"); 
  spanToDo.innerText = newToDo;

  liToDo.appendChild(spanToDo);

  toDoList.appendChild(liToDo);
}


newToDo = 'Study';
addToDo(newToDo);
```
(1) 추가할 곳의 element를 얻어온다. 이렇게 상위 element를 parent라고 하며, 추가 될 대상은 child가 될 것이다. 첫 줄에서 볼 수 있듯document.getElementById()로 얻어왔다.
  
(2) createElement로 li와 span을 만들었다. 
```HTML
...
<ul id="todo-list">
  <li>
    <span>(추가 할 텍스트)</span>
  </li>
</ul>
...
```
이렇게 추가 할 것이므로, li와 span을 불러온것임.  
  
(3) span 안에다가 (추가 할 텍스트) 를 넣는다. 
  spanToDo.innerText = newToDo;  
  
(4) spna은 \<li>의 child가 되어야 하므로, span을 넣기 위해 .appendChild();를 사용한다

(5) li는 \<ul>의 child가 되어야 하므로, li를 넣기 위해 또 다시 .appendChild();를 사용한다.

(6) toDoList는 첫줄에서 보듯 원래 HTML의 id="todo-list"인 애를 가져온 element이다.   
여기서 참 신기한점은, **"HTML을 갱신해"** 라는 어떤 매소드가 없이, 그냥 appendChild() 만 해주면 자동으로 HTML 갱신까지 해준다.  
이미 짜여진 HTML에서 element를 가져오고, 그 element의 메소드를 이용해 무언가를 추가하면, HTML이 자동으로 갱신되나보다.  


## 삭제

#### 추측

앞서 놀라웠던 부분을 다시 생각해보자. HTML에 특정 element가 존재하고 있다. 그걸 id로 가져왔다. 그다음 그 element의 method를 이용하니 HTML이 자동으로 갱신되고, 갱신된 페이지를 사용자에게 보여준다.  
  
그러면 같은 매커니즘으로 삭제도 될까? element를 가져오고, 매소드로 어떤 element를 삭제해라고 하면, 그 element가 html에서 삭제되고 HTML이 자동으로 갱신될까?  
  
### 어떻게 생긴 HTML을 만들어 내야 할까
```HTML
...
<ul id="todo-list">
  <li>
    <span>(추가 할 텍스트)</span>
    <button>❌</button>
  </li>
</ul>
...
```

### 삭제 코드
  
```JS
const toDoList = document.getElementById("todo-list");


function deleteToDo(event){
    const li = event.target.parentElement; 
    li.remove();  
}

function addToDo(newToDo){
    const liToDo = document.createElement("li");

    const spanToDo = document.createElement("span"); //<span></span>
    spanToDo.innerText = newToDo; //<span>newToDo</span>

    const deleteBtnToDo = document.createElement("button"); //<button></button>
    deleteBtnToDo.innerText = "❌"; //<button>❌</button>

    deleteBtnToDo.addEventListener("click", deleteToDo);

    liToDo.appendChild(spanToDo);
    liToDo.appendChild(deleteBtnToDo);
    
    toDoList.appendChild(liToDo);
}
```

함수 addToDo가 실행 될 때, 자동으로 삭제 버튼도 추가되도록 했다.  
앞서 span 추가한 방식과 똑같다. button을 추가하고 그 button에 eventListener를 추가해둔다. 그 다음 appendchild 하면 eventListener가 들어간 button이 추가가된다.  
그리고 deleteToDo 함수를 보자. 코드에 주석을 추가한 버전을 아래에 적어뒀다.
```JS
function deleteToDo(event){
    const li = event.target.parentElement; 
    /*
      <ul id="todo-list">
        <li>
          <span>(추가 할 텍스트)</span>
          <button>❌</button>
        </li>
      </ul>
      
      이렇게 있으면
      event의 target은 <button>이고
      <button>의 parentElement는 <li> ... </li>로 묶인 부분이다
      즉, <li> ... </li>부분을 const li 로 저장한다.
    */
    
    li.remove();  
    /* 
      li에 내장된 .remove(); 메소드를 이용해 삭제한다.
      appendChild()을 썼을 때 HTML이 자동으로 갱신 된 것과 마찬가지로, 
      remove()을 썼을 때도 HTML이 자동으로 갱신됨.
     */
}
```
[.remove() 공식문서](https://developer.mozilla.org/en-US/docs/Web/API/Element/remove)  
  
  
  
---
# localstorage에 array로 넣기

```JS
// toDos = [a,b,c]
localStorage.setItem("todos_normal",toDos);
localStorage.setItem("todos_stringify",JSON.stringify(toDos));
```
![image](https://user-images.githubusercontent.com/101965836/161188000-ec28f9ad-e1cb-4287-9975-325ae4fe85c1.png)  
  
## stringify 장점
  
![image](https://user-images.githubusercontent.com/101965836/161188560-5ee7192b-a5ad-43a2-8774-b79c376bb15b.png)  
(1) localstorage에는 string으로만 저장해야 하는데, JSON.stringify()를 사용하면 array 처럼 바꿔서 저장하고 꺼내올 수 있다. 꺼내올때는 JSON.parse(...)를 사용하면 된다.  
  
(2) 그냥 array를 넣어버리면 같은 내용을 중복해서 넣을 수 없는 문제가 생긴다. stringify는 중복된 데이터도 넣을 수 있다.  
    

---
# array에 쓰는 .filter() 메소드
  
  ![image](https://user-images.githubusercontent.com/101965836/161192806-f0850260-b838-4675-b648-4d916abccb12.png)  

이거 정말 좋아

\["a","b","c"]  
이런 array가 있다고 해보자. 여기서 b를 찾아서 지울려면?  

```JS
for (var i = 0; i < array.length; i++)
 if (array[i] == "b"){
  ...
 }
```
for문 돌리고, 값 같은 인덱스 찾아서, 어쩌고 저쩌고 빼고 삭제하고 새 array에 넣고 등등  
정말 뻔하지만 귀찮고 자질구래한 작업이 된다.  
  
## 하지만 .filter()가 출동하면?
  
.filter()를 쓰면 간단히 해결된다.  
![image](https://user-images.githubusercontent.com/101965836/161193352-4d6f7ee0-316c-4a53-b482-79cc842450eb.png)
   
## 원리는?  
   
filter()는 아래와 같이 실행된다  
  
![image](https://user-images.githubusercontent.com/101965836/161193808-89f90854-7904-4b01-83aa-026c818d0a4f.png)  
   
true인지 false인지 판단하는 함수를 주면, filter가 각 요소마다 true false를 판단한다. 그 다음 true인 놈들로만 이뤄진 array를 반환한다.  
