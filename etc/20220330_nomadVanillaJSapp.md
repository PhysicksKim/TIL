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

첫 번째 방법은 .removeEventListener() 매소드를 이용해서, 두 번째 방법은 위의 코드처럼 = undefined; 같은 방식으로 삭제해야 한다. 코드 가독성부터, 이벤트리스너 관리까지 첫 번째 방법이 더 좋다. 특히, 매소드를 이용하면 옵션을 줄 수 있는등의 선택지까지 있다.  
  
또한 첫 번째 방법으로는 여러 이벤트 핸들러를 추가할 수 있다는 장점이 있다.  
