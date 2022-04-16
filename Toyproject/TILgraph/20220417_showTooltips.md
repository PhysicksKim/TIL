
![녹화_2022_04_17_01_45_38_19](https://user-images.githubusercontent.com/101965836/163684254-04855279-0096-4056-8791-7101df64738a.gif)  
구현한 내용


# 개요 
#### : 뭘 하려 했는가?
각각 일정 요소에 마우스를 올리면, 해당 일정에서 특정 값이 Tooltip으로 표시 되도록 구현하려 함.  
FullCalendar에다가 React-Tooltip을 씌워서 Tooltip을 표시하려함.  

## 그럼 기존에 어떻게 구성되어 있었는가?
(1) github 내 TIL 리포지에서 READMD.md를 불러와서 데이터 정리 -> TIL 데이터  
(2) TIL 데이터를 바탕으로 FullCalendar api 이용해서 캘린더에다가 언제부터 언제까지 뭐 배웠는지 띄움  
  
  
## 어떤 구성을 추가해야 했는가?
(3) 캘린더에 그려진 각각 요소에다가 마우스 올리면, tooltip이 표시되도록 하려함  
  
---  
  
<area />  
  
---  
  
# API 사용 방법 설명
## 1. FullCalendar 사용법
FullCalendar는 데이터 형식 맞춰서 통째로 데이터만 넘겨주면, 알아서 잘 캘린더를 만들어 줌.  
```js
return(
  <>
    <FullCalendar
      plugins={[ dayGridPlugin ]}
      initialView="dayGridMonth" 
      events={props.calendarInputData}
    />
  </>
)
```
```js
calenderInputData = [
  { title : 'ex1', start: '2022-04-16', end: '2022-04-17' },
  { title : 'ex1', start: '2022-04-14', end: '2022-04-16' },
  ...
]
```

## 2. React-Tooltip 사용법
React-Tooltip은 <ReactTooltip /> 이라고 컴포넌트 넣어주고, 다른 일반적인 html 태그에다가 data-tip="툴팁내용" 적으면 그냥 툴 팁 표시해줌  
```js
<ReactTooltip />
<p data-tip="hello world">Mouse Over Here</p>
```

# 근데 FullCalendar 안에다가 어떻게 data-tip={title}을 삽입하지?
이거 때문에 많이 헤맸다   
나는 그냥 data를 FullCalendar에다가 넘겨주기만 했고, 태그 구성은 FullCalendar가 알아서 했는데...  
React-Tooltip으로 tooltip 띄울려면 직접 태그를 써서  
<어쩌고태그 data-tip="어쩌고툴팁"> 이라고 써넣어란다...  
  
  
어떻게 캘린더에 등록된 일정에다가 각각 직접 태그를 삽입하는거지?  
  
# 내가 원하는 동작 
WEB2-Javascript 라는 강의가 캘린더에 있다고 해보자.  
그러면 캘린더에서 한 블럭 차지하는 WEB2-Javascript는 아마도 아래와 같이 짜여져 있을 것이다.  
```HTML
<태그들>
  <이제 제목 들어감>
    WEB2-Javascript
  </이제 제목 들어감>
</태그들>
```
  
  
나는 이렇게 만들어질 FullCalendar 태그를 tooltip용으로 바꿔야 한다. 아래 예시와 같이 말이다.  
```HTML
<태그들>
  <이제 제목 들어감>
    <p data-tip="WEB2-Javascript">WEB2-Javascript</p>
  </이제 제목 들어감>
</태그들>
```

단순 제목이 들어가는 대신 태그가 들어가면된다.  
이거에 대해서 한참 찾았는데, 해당 방법을 찾았다.

> 직접 FullCalendar부분 html까지 까봤다
> ```
> <div class="FullCalendar">
>   <div class="name1">
>     <div class="name2">
>       ...
>         <td ... >
>           <div>
>             TIl 각 요소
>           </div>
>         </td>
>       ...
>     </div>
>   </div>
> </div>
> ```
> 대충 이런식으로 생겼더라. 
> 최종 로딩된 HTML DOM에서... 다시 접근해서 Tooltip을 위한 태그를 작성할까? 라고도 생각했지만
> 아마 FullCalendar에 각 요소에다가 태그를 집어넣는 방법이 있을꺼야... 라고 생각하고 계속 찾았다.  

# FullCalendar의 eventContent={...}
[Content Injection이란? - FullCalendar 공식 문서](https://fullcalendar.io/docs/content-injection)   
[React에서 Content Injection - FullCalendar 공식 문서](https://fullcalendar.io/docs/react#content-injection)   
  
  
content injection이란 한마디로, "니가 원하는 대로 DOM 통째로 넣어줌" 이다.  
딱 내가 찾던 것이다.  
이걸 어떻게 쓰냐면
```
<FullCalendar
  ...
  eventContent={ (info)=>{...} }
/>
```
이런식으로 쓰면 된다.  
캘린더에 각 일정 요소 데이터들이 삽입 될 때, 원래대로라면 데이터의 title이 그냥 텍스트로만 삽입된다.   
그런데 eventContent 안의 callback 함수에다가 return으로 JSX 문법을 통해서 태그를 써넣어 주면,  
그 태그 그대로 각 요소 최 하위에 삽입된다.  

# 어떻게 구현했나

```js
eventContent={(info)=>{
  return(
    <p data-tip={info.event.title}>
        {info.event.title}
    </p>
  )
}}
```
인자로 받는 info는 각 요소들을 만들 때 데이터를 담은 객체다.  
위 처럼 코드를 구성하면 아래와 같이 각 요소들에다가 태그가 쭉쭉 삽입되게 된다.

```
<태그들>
  <이제 제목 들어감>
    <p data-tip="WEB2-Javascript">WEB2-Javascript</p>
  </이제 제목 들어감>
</태그들>
```

# 그런데 문제 발생
아무튼 아래와 같이 코드를 넣었다
```
<FullCalendar
  plugins={[ dayGridPlugin ]}
  initialView="dayGridMonth" 
  events={props.calendarInputData}
  eventContent={(info)=>{
    return(
      <p data-tip={info.event.title}>
        {info.event.title}
      </p>
    )
  }}
/>
```
근데 안되더라.  
개발자 도구로 HTML도 봤는데, 전부 정상적으로 위 처럼 data-tip={}이 들어가 있다.  
혹시나 react-tooltip 문법이 잘못됐나 싶어서, 제일 바깥에다가 <p data-tip="hello tooltip">Over Here</p> 라고 써서 테스트 해봤다.  
문법 문제는 아니었다. 이렇게 임의로 만든 테스트용 태그는 잘 되더라.  
근데 FullCalendar의 각 요소는 저 data-tip을 갖고 있는데도 안된다. 왤까...  
  
  
그렇게 하다가 찾았다  

# .rebuild()
![image](https://user-images.githubusercontent.com/101965836/163686185-9f3e03c2-6605-4f96-82ce-9481cc68e3a8.png)  
[react-tooltip 페이지](https://github.com/wwayne/react-tooltip)  
차근히 react-tooltip의 설명을 읽어봤다. 이제는 태그는 넣었는데 동작하지 않으니 FullCalendar의 문제가 아니라 React-Tooltip의 문제니까.  
그러다가 Static Method(정적 매소드)에서 rebuild()가 눈에 띄었다.  
  
  
> Rebinding all tooltips ?????
> 어? Rebinding이 뭐지?
  
이때부터 뇌피셜 회로가 막 돌아갔고, 이러한 결론을 내렸다.  

> <ReactTooltip />을 하면
> 저 컴포넌트가 HTML에서 data-tip 이란게 들어간 태그들을 싹 찾고,
> 이를 바탕으로 어떤 태그들에 tooltip을 띄워야 하는지 컴포넌트 안에 저장해두나보다  
> 근데 rebinding?
> 아 뭔가 bind가 관계를 연관지어 주는걸 말하는 거니까
> 저 컴포넌트 안에서는 tooltip 띄우는 창과 해당 태그가 binding 되어 있는 식으로 동작하는구나!
> 어...
> 그러면 
> 새로 DOM 요소에 Tooltip을 위해 data-tip 프롭스를 넣었으면
> ReactTooltip은 얘랑 binding 되어 있지 않겠구나
> 그럼 binding을 연결 짓는 명령을 시행해야 하는데
> 그게 .rebuild()인가보다!!!

이렇게 순식간에 싹 뭔가 느낌이 왔고  
곧바로 다음과 같이 추가했다

```JSX
return(
<>
  <ReactTooltip />
  <FullCalendar
    plugins={[ dayGridPlugin ]}
    initialView="dayGridMonth" 
    events={props.calendarInputData}
    eventContent={(info)=>{
      return(
        <p data-tip={info.event.title}>
            {info.event.title}
        </p>
      )
    }}
    eventMouseEnter={()=>{
      ReactTooltip.rebuild();
    }}
  />
</>
);
```

### 됐다! 드디어 됐다! 🎉🎉🎉

저 rebuild()를 해줘야 했던거다...
FullCalendar는 최초에 ReactTooltip이 생성될 때 보다 더 뒤에 생성된다.  
버튼을 누르면 데이터 받아와서 캘린더에 적용되도록 구현했기 때문이기도 하지만, 어차피 웹페이지 실행되자마자 데이터 바로 받아오도록 했다 하더라도 비동기니까 사실상 ReactTooltip 컴포넌트가 생성될 때 보다 더 늦게 생성될것이니 말이다.  
아무튼  
최초에는 ReactTooltip 컴포넌트에서 캘린더 안에 있는 data-tip을 가진 놈들이 뭐가 있는지 몰랐으니까 툴팁이 안떳던 것이다.  
그래서 캘린더 띄우고 ReactTooltip.rebuild()를 해주면 됐던 것이고.  

# 사실 eventMouseEnter에다가 rebuild()하면 최적화 낭비임  
당연히 rebinding 작업은, tooltip용 태그가 추가됐을 때 한번 돌려주면 땡이니까, 매번 할 필요가 없다.  
보니까 fullcalendar에 eventDidUpdate 같은게 있던데, 여기다가 넣어주면 될 것 같다.  
