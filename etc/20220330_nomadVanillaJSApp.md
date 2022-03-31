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
