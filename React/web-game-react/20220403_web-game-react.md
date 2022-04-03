# 1. 리액트 기본 튜토리얼 + 구구단  
  
리액트 쓰는 이유  
1. SPA 구현 등 UX가 좋도록 만들기 용이함  
2. 컴포넌트들을 재사용 할 수 있음(함수 재사용 하는 것 처럼)  
3. 데이터와 화면을 일치시키기 용이함  
  
리액트에서 JSX 문법을 지원하기 위해 babel을 사용한다.  
바벨을 사용하기 위해서는 \<script type="text/babel">이라고 적어야 한다.  
  
바뀌는건 **State** 다. 즉, 웹 페이지에서 바뀌는 부분이 있으면 그건 다 State로 처리할 수 있다.  
state로 관리하면 이점이 있다. 예를 들어 input으로 text 입력 창이 있다고 해보자.   
여기서 사용자가 내용을 입력하고 submit 버튼 누르고 나서도 JS로 그 내용을 받을 수 있다.  
하지만 State로 변화가 감지될 때마다(ex.onChange) setState 해주면, 지속적으로 변수에 입력 값을 갱신해 받을 수 있다.  
 
태그 사이에 \<div>{}곱하기{}는?\</div> 이렇게 {}를 넣으면 그 안에 JS를 넣을 수 있다.    
  
setState는 자동으로 바뀌는 부분에 넣으면 안되고, 수동으로 바뀌는 부분에 넣어야 한다. (이유는 강의 나중에 설명)  
  
자바스크립트로 이벤트 적을 때는 onClick onSubmit 처럼 lowerCamelCase로 처음은 소문자 그다음 부터는 대문자로 표현해야 한다.  
  
class 안에서 메소드 만들 때  
```JS
class GuGuDan extends ReactComponent{
  ...
  onSubmit = function (e) { ... }
 
  onSubmit = (e) => { ... }
  ...
}
```
function 쓴거랑 화살표 함수 쓴거랑 this가 달라져 버린다.  
매소드는 무조건 화살표 함수로 써야지 class가 this로 지정된다.  


#### 궁금한 점 - ref의 생성법 2
오토포커스 (1-10. ref 강의) 추가 할 때, ref 라는 것을 사용했다. 그런데 검색해서 나온 ref 사용법이랑 좀 달라서, 뭔 차이인지 궁금해졌다.  

##### (1)강의에서 쓴 방법
```JS
class GuGuDan extends React.Component {
  constructor(props){ 
    ...
  }
  
  onSubmit = (e) => {
    ...
    
    this.input.focus();
    
    }
    
  ...
  
  input;
  
  ...
  
  render(){ 
    return ( 
      <div>
        ...
        
        <form onSubmit={this.onSubmit}>
          <input ref={(c) => {this.input = c;}} type="number" ... />
          
        ...
    );
  }
}
```
class 안에 그냥 쌩짜로 input;이라고 적어두고  
input 태그 안에다가 (c) => {this.input = c;}} 라고 적어뒀다.   
이렇게 하면 input의 ref가(DOM 요소가) this.input으로 전달되고,  
얻어낸 input DOM 요소를 바탕으로 this.input.focus(); 로 focus 잡아주는 매소드를 실행시키나 보다.  

##### (2)검색해서 나온 방법
```JS
class GuGuDan extends React.Component {
  constructor(props){
    ...
    this.ref = React.createRef();
  }

  onSubmit = (e) => {
    ...

    this.ref.current.focus();

  }
  
  ...
  
  render(){ 
    return ( 
      <div>
        ...
        
        <form onSubmit={this.onSubmit}>
          <input ref={this.ref} type="number" ... />
          
        ...
    );
  }
}
```
this.ref를 React에 있는 .createRef()로 생성한다.  
그리고 input 태그 안에서 ref={this.ref}로 input의 ref가 this.ref가 되도록 설정한다.  
그다음 ref를 쓸 때, 앞과 같이 focus()를 쓰는데, ref에서 current로 한 번 더 들어가야 dom 요소가 된다. 
   
**이렇게 둘이 정확히 뭔 차이이고, 각각 어떤 원리인지는 아직 잘 모르겠다**  

=> 검색해서 찾은 결과, 선언 방식의 차이이며 createRef가 그냥 최신 버전(React v16.3)에 추가된 기능이라고 한다. createRef를 통해 좀 더 간결하게 코드를 만들 수 있다고 한다. [공식문서](https://ko.reactjs.org/docs/refs-and-the-dom.html)  
[강의 방법](https://thebook.io/080203/ch05/02/01/)  [creatRef 방법](https://thebook.io/080203/ch05/02/02/)  



  
render()는 너무 자주 일어나지 않도록 주의하는게 좋다. 나중에 프로젝트가 커지면 랜더를 너무 자주 반복해서 문제가 생길 수 있다. 좀 더 설명하자면, setState를 한 번 할때마다 render()가 다시 실행되어서 문서를 갱신한다. 그런데 만약에 render안에 오래 걸리는 작업이 있다면, 이는 지나치게 버벅거리는 문제가 생길 수 있다. 그러므로 render()가 자주 일어나지 않도록 하는 게 좋다. 
