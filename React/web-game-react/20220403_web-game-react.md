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
(검색 키워드 : createRef사용법 )  
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
  
  
  ---
  
# 2. 리액트 Hooks 
# 2-1 구구단 hooks로 바꿔서 코딩

## hooks란? 탄생 배경은?

```HTML
<div id="root">
    <script type="text/babel">
        const Gugudan = () => {
            return <div>Hello, Hooks</div>
        }
    </script>

    <script type="text/babel">
        ReactDOM.render(<Gugudan/>, document.querySelector('#root'));
    </script>
</div>
```
class에서는 render도 들어가고 선언문도 들어가서 코드가 복잡했다.  
하지만 함수형으로 선언하면 위 코드처럼 간결하게 표현할 수 있다.  
따라서 react에서 함수형을 통해 컴포넌트를 선언할 수 있도록 지원하게 된 것이 바로 hooks이다.  
  
  
## state 선언 방법

#### 기존 class 방식
```JS
class GuGuDan extends React.Component {
  constructor(props){
      super(props);
      this.state = {
          first: Math.ceil(Math.random() * 9),
          second: Math.ceil(Math.random() * 9),
          value: '',
          result: '',
      };
      this.ref = React.createRef();
  }
}
```

#### Hooks 방식
```JS
const Gugudan = () => {
    const [first, setFirst] = React.useState(Math.ceil(Math.random() * 9));
    const [second, setSecond] = React.useState(Math.ceil(Math.random() * 9));
    const [value, setValue] = React.useState('');
    const [result, setResult] = React.useState('');
}
```

## hooks로 구구단 만들어 보기
use로 시작하는 애들이 hooks인데 함수 컴포넌트에 hooks 추가해서 추가된 것이다.  

또한 코드량도 hooks가 더 적다.  

일단 return 부분은 
```JS
return (
    <React.Fragment>
        <div> {first} 곱하기 {second}는? </div>
        <form onSubmit={}>
            <input ref={} onChange={} value={value} />
            <button>입력!</button>
        </form>
        <div id="result">{result}</div>
    </React.Fragment>
);
```
이렇게 쓸 수 있다. 이제 여기서 ref onChange onSubmit 부분을 채워나가자.  

### ref 부분 구현을 살펴보자
```JS
const Gugudan = () => {
  const ref = React.useRef(null);
  
  ...
  
  if (...) {
    ref.current.focus();
  }
  
  ...
  
  return (
    ...
      <input ref={ref} ... />
    ...
  );
```
useRef로 그냥 선언하면 되고, 사용하기 위해 DOM요소에 접근 할 때는 ref.current로 접근해야한다. 이외에도 선언할때 지금은 null로 넣었지만 초기값도 줄 수 있다고 한다.  
   
   
   
### onChange 부분 구현을 살펴보자
  
#### class때 쓰던 방식
```
onChange = (e) => { this.setState({value: e.target.value}) }
```
#### hooks 방식으로 쓰기
```
const onChangeInput = (e) => { setValue(e.target.value); };
```

setState를 쓸 필요 없이, 위에서 \[value,setValue] 에서 setValue를 바로 쓰면 된다.  
setState에서는 setState하고 어떤 값을 바꿀지 {value : ...} 같은 식으로 정해줬다.  
하지만 hooks에서는 그냥 value 전용 값 바꾸는 장치인 setValue를 선언해줬고,  
이걸 그대로 가져다가 setValue(...)으로 해주면 된다. 훨씬 간결한 코드가 됐다.  
  
### onSubmitForm 부분 구현 살펴보자

#### class때 쓰던 방식
```JS
onSubmit = (e) => {
  e.preventDefault();
  if (parseInt(this.state.value) === this.state.first * this.state.second){
      this.setState({
          result: `${this.state.first} x ${this.state.second} = ${String(this.state.first * this.state.second)} 정답!`,
          first: Math.ceil(Math.random() * 9),
          second: Math.ceil(Math.random() * 9),
          value: '',
      });
      this.ref.current.focus();
  } else {
      this.setState({
          result: `${this.state.first} x ${this.state.second} ≠ ${this.state.value} 땡!`,
          value: '',
          first: Math.ceil(Math.random() * 9),
          second: Math.ceil(Math.random() * 9),
      });
      this.ref.current.focus();
  }
}
```
계속 this.state가 반복되니까 지저분해진다.  
  
  
#### hooks 방식
```JS
const onSubmitForm = (e) => {
    e.preventDefault();
    if (parseInt(value) === first * second){
        setResult(`${first} x ${second} = ${String(first * second)} 정답!`);
        setFirst(Math.ceil(Math.random() * 9));
        setSecond(Math.ceil(Math.random() * 9));
        setValue('');

        ref.current.focus();
    } else {
        setResult(`${first} x ${second} ≠ ${value} 땡!`);
        setValue('');
        setFirst(Math.ceil(Math.random() * 9));
        setSecond(Math.ceil(Math.random() * 9));

        ref.current.focus();
    }
}
```
훨씬 간결하게 바뀌었다.   
또 줄마다 set\*** 이라고 해서 어떤 값을 set중이라는 것을 말하고 있다. 사람마다 다르겠지만 나는 이것도 가독성 향상에 도움된다고 생각한다. 

## 추가 : setState 할 때 옛날 값을 쓰는 경우
```JS
setResult('정답!');

setResult( (prevResult) => { return '정답: ' + value } )
```
근데 왜 옛날값을 쓰는가? 이건 솔직히 잘 모르겠다. 그리고 prevResult를 인자로 받아왔는데, 왜 prevResult를 쓰지 않고 그냥 value를 쓰는거지? 강사가 착각했나? 잘 모르겠다.  


## 정리
1. hooks 쓰면 코드가 짧고 간결해진다.  
2. hooks는 state를 바꾸면 함수 자체가 통째로 다시 실행되기 때문에, 느릴 수 있다. 반면 class방식에서는 render()함수만 다시 실행된다.  


## 추가 : 알아둬야 할 정보
**Hooks에서 setState는 비동기이다.**  
예를 들어
```
setResult(`${first} x ${second} ≠ ${value} 땡!`);
setValue('');
setFirst(Math.ceil(Math.random() * 9));
setSecond(Math.ceil(Math.random() * 9));
```
위에 이런 코드가 있다.
그러면 set뭐뭐뭐 할 때마다 render() 작업이 일어날까? 아니다  
set작업은 react가 알아서 보고 판단해서 비동기적으로 처리해준다.  
즉, hooks의 함수가 한 번 실행될 때마다, render도 한 번 실행된다.  

  
#### 물론 그래서 발생하는 문제도 있다.  

예를 들어
```JS
// num = 0;
const increseNum = (e) =>{
  setNum(num + 1);
  setNum(num + 1);
  setNum(num + 1);
  setNum(num + 1);
}
```
이렇게 있으면 hooks 함수가 한 번 돌고나면 num은 얼마나 증가할까?  
num은 1이 된다.   
  
  
(이 밑으로 뇌피셜)    
첫 setNum에서 num도 0이다
두 번째 setNum에서 num도 0이다.
... 전부 setNum에서 num은 0이다. 
  
이렇게 setNum에 num값들이 전부 0으로 담겨서 전달되고,
결국 
```JS
setNum(0+1); 
setNum(0+1); 
setNum(0+1); 
setNum(0+1); 
```
이렇게 보낸거랑 같은 꼴이다.  
이를 해결하기 위해서는 prev를 인자로 받아서 사용해야 한다.  
자세한 내용은 [여기](https://www.delftstack.com/howto/react/react-setstate-prevstate/) 를 참고.  


---

# 2-2. 끝말잇기 시작 - 세팅

## 웹팩 설치하기
코드가 길어지면, 컴포넌트도 엄청 많아지고, js 파일끼리 스크립트간 중복도 발생한다.  
그러다가 천재가 Webpack을 만든다. 
실무에서 최소 수백, 수천개의 자바 파일로 나뉘는데 나중에 가면 당연히 말도 안되게 중복이 발생한다. 이 때 여러개의 js파일을 하나의 js로 합쳐주는게 바로 웹팩이다.   

### 설치과정
#### 가장 간단한 방법
creat-react-app을 쓴다.
하지만 이렇게만 배워버리면 어떤 과정으로 세팅이 되는지 이해하지 못한다. 따라서 자동으로 하는 create-react-app에만 의존하지 말고, 직접 세팅하는 과정을 따라가보도록 하자.  
  
#### 직접 세팅 해보기  
\1. 설치할 폴더로 들어간다  
\2. CLI에서 아래 명령어를 친다  
2-1. npm init    
package name은 적당히 이름 적어넣고 전부 엔터엔터엔터, author나 license는 적당히 자기 이름적고 MIT 라이센스로 둔다.    
그러고 나면 init을 한 폴더에 package.json 파일이 만들어 진다.    
2-2. npm i react react-dom    
react랑 react-dom을 설치한다.   
2-3. npm i -D webpack webpack-cli  
-D는 개발에서만 쓴다 라고 하는 옵션이다. webpack이랑 webpack-cli를 설치해 준다.    
\3. webpack.config.js 라고 파일을 만들어 주고 아래 코드를 적어넣어준다.  
```JS
module.exports = {
    
};
```  
\4. client.jsx 라고 파일을 만들어 주고 아래 코드를 적어넣어준다.   
```JS
const React = require('react');
const ReactDom = require('react-dom');
```  
이전에는 HTML에서 일일이 react 불러오고 babel 불러오고 했지만, 이제는 require를 이용해서 그냥 불러올 수 있다.    
그리고 여기서 jsx라는 확장자라고 한 이유는, 앞서 babel 처음 도입할 때 말했듯 jsx 문법을 쓰기 위함이다.   
\5. index.html 만들어서 아래와 같이 입력해준다.    
```HTML
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>끝말잇기</title>
</head>
<body>
    <div id="root"></div>
    <script src="./dist/app.js"></script>
</body>
</html>
```
여기서 유의할 점은 root와 아래 script코드의 src="./app.js"는 꼭 있어야 한다.  
app.js라는게 나중에 웹팩으로 합칠 js 파일이고, 위에 코드에서는 그걸 dist 경로 안에다 만들거기 때문이다.  
(합치는 이유 : 각자 다른 파일에 있던 컴포넌트들을 따로따로 script 구문으로 불러오면 HTML이 인식을 못한다. 그래서 app.js라는 하나의 파일로 합쳐줘야지만 HTML이 인식을 하고 동작한다)  
   
## 모듈 시스템과 웹팩 설정

### JS파일 분리해서 코드 작성하고, 불러오는법
```JS 
const React = require('react');

...

module.exports = WordRelay;
```
위의 require은 해당 파일에서 필요로 하는 패키지나 라이브러리를 가져오는 부분.  
아래는 쪼갠 파일의 컴포넌트를 바깥에서도 쓸 수 있게 하는 부분.  

다른 파일에서 불러올때는
```JS
const WordRelay = require('./WordRelay');
```
이렇게 코드 처음에다가 추가해주면 된다.  
  
### webpack 설정하기
```JS
const path = require('path');

module.exports = {
    name: 'wordrelay-setting',
    mode: 'development',
    devtool: 'eval',
    resolve: {
        extensions: ['.js','.jsx'], // resolve에서 extensions로 하고 확장자들을 적어주면, 아래의 entry에서 일일이 파일마다 확장자 명을 적어주지 않아도 자동으로 인식하고 합쳐준다. 
    },
    entry: {
        app: ['./client'], //client.jsx에서 이미 const WordRelay = require('./WordRelay');로 WordRelay.jsx를 불러왔기 때문에 꼭 넣을 필요없이 webpack이 알아서 포함해준다. 

    },  // 입력
    output: {
        path: path.join(__dirname, 'dist'), // 이렇게 하면 (__dirname) = 현재폴더, 'dist' = 하위 폴더 로 지정할 수 있게 된다.
        filename: 'app.js',
    },  // 출력
};
```
위의 주석 참고  
  
## 웹팩으로 빌드하기

그냥 webpack 하면 빌드가 안된다. 왜냐? 그게 무슨 명령인지 등록을 안했기 때문이다.  
여기서 명령을 등록하지는 않고, npx라는걸 사용한다.
```
npx webpack
```
>npx는 node_modules아래에 있는 .bin 디렉터리의 실행파일을 직접 접근하지 않고도 쉽게 사용할 수 있게해줍니다.
[출처](https://velog.io/@duplicareus/%EC%9B%B9%ED%8C%A9%EC%9D%B4-%EB%AD%94%EB%8D%B0)  
  
#### 에러
```
KHC@DESKTOP-TNMP2Q4 MINGW64 /g/MyHistory/Learning/inflearn/web-game-react/lecture (main)
$ npx webpack
asset app.js 1.53 KiB [compared for emit] (name: app)
./client.jsx 184 bytes [built] [code generated] [1 error]

ERROR in ./client.jsx 6:16
Module parse failed: Unexpected token (6:16)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| const WordRelay = require('./WordRelay');
|
> ReactDom.render(<WordRelay />, document.querySelector('#root'));

wordrelay-setting (webpack 5.71.0) compiled with 1 error in 95 ms
```
여기서 에러가 난 이유는 <WordRelay /> 이 부분이 jsx 문법인데, 이를 인식하지 못했기 때문이다. 그러면 어떻게 해줘야 하느냐? webpack이 빌드 할 때 바벨을 인식하지 못한것이므로, webpack 세팅하는 webpack.config.js 파일에다가 어떤 모듈을 적용해야하는지 적어주면 된다.   
  
<br>  
  
#### 우선 바벨 설치
```
npm i -D @babel/core @babel/preset-env @babel/preset-react babel-loader
```

#### 그 다음, webpack.config.js 설정
```js
entry: { ... },

module: {
    rules: [{
        test: /\.jsx?./, //정규 표현식인데 정확히 알려면 따로 공부해야 할 정도로 복잡한 내용이다. 이건 js파일과 jsx파일에 이 룰을 적용한다는 의미다. 
        loader: 'babel-loader',
        options:{
            presets: ['@babel/preset-env', '@babel/preset-react'],
        },
    }],
}, // entry에 파일을 읽은 후, module을 적용해서, output으로 파일을 만들어 낸다
 
output: { ... },

```
위와 같이 entry와 output 사이에 module:{} 을 추가해준다. babel을 사용하겠다는 내용이다.  



# 2-3 끝말잇기 만들기

## 코드 변경 발생할 때마다 자동으로 빌드 (핫 리로딩)
(1) 필요한 것들 설치
```
npm i react-refresh @pmmmwh/react-refresh-webpack-plugin -D
npm i -D webpack-dev-server
```
(2) 설정 파일 내용 수정 및 추가
```JSON
// package.json
  "scripts": {
    "dev": "webpack serve --env development"
  },
```
```js
//webpack.config.js
const RefreshWebpackPlugin = require('@pmmmwh/react-refresh-webpack-plugin');

...

module.exports = {
  module: {
    rules: [{
      
      ... ,
      
      options:{
            ... ,
            plugins: [
                'react-refresh/babel',
            ]
      },
    }],
  },
  
  ...
  
  plugins:[
          new RefreshWebpackPlugin()
  ],
  
  ...
  
  devServer: {
        devMiddleware: {
            publicPath: '/dist', // webpack의 output 파일이 있는 경로
        },
        static: {
            directory: path.resolve(__dirname), // index.html이 존재하는 경로
        },
        hot: true,
    },
  
  ...

};
```
  
## 리로딩과 핫 리로딩 차이
리로딩 : 코드 수정하면 자동 리로딩 되지만 처음부터 다시 시작   
핫 리로딩 : 코드 수정하면, 진행중이던 단계에서 부터 다시 시작    
  
  


## 추가: ReactDOM.render is no longer supported in React 18

react 18부터는 createRoot() 해서 root.render()를 쓰라고 한다. 사용법을 알아보자.

```JS
// 이전 버전 코드  
const React = require('react');
const ReactDom = require('react-dom');

const WordRelay = require('./WordRelay');

ReactDom.render(<WordRelay />, document.querySelector('#root'));
```
```JS
// root 방식
const React = require('react');
import { createRoot } from 'react-dom/client'; //  createRoot을 import 하고

const WordRelay = require('./WordRelay');

const container = document.querySelector('#root'); //  root의 dom 가져 온 다음
const root = createRoot(container); //  createRoot() 메소드에 root dom 넘겨줘서

root.render(<WordRelay />); //  root.render() 메소드로 react 컴포넌트 넘기면 됨
```

[출처 공식문서](https://reactjs.org/blog/2022/03/08/react-18-upgrade-guide.html)  
  
  
  ---
  
# 3. 숫자야구  

## 시작 전, Hot Module Replacement is not enabled! 에러  
  
```
KHC@DESKTOP-TNMP2Q4 MINGW64 /g/MyHistory/Learning/inflearn/web-game-react/ch3/NumberBaseball (main)
$ npx webpack
<w> [ReactRefreshPlugin] Hot Module Replacement (HMR) is not enabled! React Refresh requires HMR to function properly.
asset app.js 1.52 MiB [emitted] (name: app)
runtime modules 3.19 KiB 7 modules
modules by path ./node_modules/core-js-pure/ 119 KiB 110 modules
modules by path ./node_modules/@pmmmwh/react-refresh-webpack-plugin/ 52.4 KiB 23 modules
modules by path ./node_modules/html-entities/lib/*.js 81.3 KiB 4 modules
modules by path ./node_modules/react-dom/ 992 KiB 3 modules
modules by path ./*.jsx 7.03 KiB 2 modules
modules by path ./node_modules/react-refresh/ 20.2 KiB 2 modules
modules by path ./node_modules/react/ 85.7 KiB 2 modules
modules by path ./node_modules/scheduler/ 17.3 KiB 2 modules
+ 3 modules
numberbaseball (webpack 5.71.0) compiled successfully in 1511 ms  
```

## HMR 세팅
#### 설치  
```
npm install --save-dev html-webpack-plugin
```
[참고](https://webpack.js.org/guides/hot-module-replacement/)  

#### webpack.config.js에 추가
```
...
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {

  ...

  
  plugins: [
    new HtmlWebpackPlugin({
      title: 'Hot Module Replacement',
    }),
  ],

  ...
  
};
```
  
  
# 3-1. import와 require 비교
import 문법은 ES2015에서 새로 추가된 문법이다.   

(1) import와 require를 같이 쓸 수 없다.  
서로 형식 자체가 다르기 때문에, require 쓸꺼면 require 방식 문법만, import 쓸거면 import 방식 문법만 써야한다. 

(2) import 쓸때와 require 쓸 때 export 하는 부분 문법이 다르다.  

#### require 쓸 때
```JS
const React = require('react');

...

module.exports = NumberBaseball;
```
#### import 쓸 때
```JS
import { React } from 'react';

...

export default NumberBaseball;
```

(3) 그래도 어쨌거나 둘 다 하는 일은 거기서 거기다.  
그럼 왜 알아야 하느냐? import로 된 코드도 알아봐야하기 때문이지.

## babel이 근데 호환을 해냅니다...
webpack 설정할 때 babel을 추가했었다. babel이 무엇이냐? 
> Babel is a toolchain that is mainly used to convert ECMAScript 2015+ code into a backwards compatible version of JavaScript in current and older browsers or environments.
[출처 What is Babel?](https://babeljs.io/docs/en/)  
  
즉 ES2015 이후인 import와 이전인 require를 호환시켜주기 때문에, 똑똑하게도 우리가 require import를 섞어서 써도 babel이 알아서 이를 호환시켜 주는 것이다. 아주 굳.   
  
  
달리 말하면, babel이 적용되지 않는, webpack.config.js에서는 import 방식으로 쓰면 안된다. webpack.config.js는 설정 파일이므로 node가 해석하기 때문이다.   
  
  
# 3-2 숫자야구 구현 - 리액트 반복문(map)
  
예를 들어 보자  
```HTML
<>
    <h1>{this.state.result}</h1>
    <form onSubmit={this.onSubmitForm}>
        <input maxLength={4} value={this.state.value} onChange={onChangeInput} />
    </form>
    <div>시도: {this.state.tries.length}</div>
    <ul>
        <li>Like</li>
        <li>Like</li>
        <li>Like</li>
        <li>Like</li>
        <li>Like</li>
        <li>Like</li>
        <li>Like</li>
        <li>Like</li>
    </ul>
</>
```
숫자 야구에서 시도 횟수가 늘어나면, 위처럼 말도 안되는 하드코딩이 될거다.  
react에서는 이때 반복문으로 map을 사용한다.  
   
예를 들어 아래와 같이 적으면  
```HTML
<ul>
  {['사과', '바나나', '포도', '귤', '수박', '메론' ].map( (v) => {
    return (
      <li>{v}</li>
    );
   })}
</ul>
```
```HTML
<ul>
  <li>사과</li>
  <li>바나나</li>
  <li>포도</li>
  <li>귤</li>
  <li>수박</li>
  <li>메론</li>
</ul>
```
v 안에 배열 [] 안의 각 요소들이 순서대로 들어가서 반복되게 된다.  
처음에 v에 '사과'가 들어가서 시행되고, 그 다음에는 v에 '바나나'가 들어가서 돌고 ... 배열의 요소 끝까지 반복되게 된다.  
  
# 3-3 숫자야구 구현 - 리액트 반복문

그러면 반복되는 요소가 2개 이상이면 어떻게 해야 할까?  
배열을 2차원으로 만들거나 객체로 만들어서 활용하면 된다.  
## 2차원 배열 방법
```html
<ul>
  {[
  ['사과', '달다'], 
  ['바나나','맛있다'], 
  ['포도','시다'], 
  ['귤','시다'], 
  ['수박','맛없다'], 
  ['메론','너무달다'] 
  ].map( (v) => {
    return (
      <li><b>{v[0]}</b>는 {v[1]}</li>
    );
   })}
</ul>
```
```html
<ul>
  <li>사과는 달다</li>
  <li>바나나는 맛있다</li>
  <li>포도는 시다</li>
  <li>귤는 시다</li>
  <li>수박는 맛없다</li>
  <li>메론는 너무달다</li>
</ul>
```
## 객체 만들기
```html
<ul>
  {[
  { fruit: '사과',   taste : '달다'}, 
  { fruit: '바나나', taste : '맛있다' }, 
  { fruit: '포도',   taste : '시다'}, 
  { fruit: '귤',     taste : '시다'}, 
  { fruit: '수박',   taste : '맛없다'}, 
  { fruit: '메론',   taste : '너무달다'}, 
  ].map( (v) => {
    return (
      <li><b>{v.fruit}</b>는 {v.taste}</li>
    );
   })}
</ul>
```
```html
<ul>
  <li>사과는 달다</li>
  <li>바나나는 맛있다</li>
  <li>포도는 시다</li>
  <li>귤는 시다</li>
  <li>수박는 맛없다</li>
  <li>메론는 너무달다</li>
</ul>
```

## Key 에러
![image](https://user-images.githubusercontent.com/101965836/162343588-31cbf255-5da2-471a-8e71-7e475828b108.png)  
위 처럼 코드를 작성했으면 콘솔 창에 이렇게 떴을거다.    
이거는 **Key** 라고 컴포넌트마다 고유한 값을 갖도록 해서 react가 나중에 성능 최적화를 할 수 있도록 하는 것이다.  
그러면 key를 어떻게 설정해줘야 할까?

### key 문법
문법은 단순하다
```JS
return (
      <li key={뭐뭐뭐}><b>{v.fruit}</b>는 {v.taste}</li>
    );
```
라고 해주면 된다. 근데 여기서 **key는 고유한 값을 갖도록** 하는 게 까다롭다.  
예를 들어 아래와 같이 **화살표 함수의 인자를 통해 index를** 얻을 수 있기도 하다. 
```JS
[ ... ].map( (v, i) => {
return (
      <li key={i}><b>{v.fruit}</b>는 {v.taste}</li>
    );
}
```
이렇게 하면 key=i가 key=0,key=1,key=2 ... 하는 식으로 계속 들어갈 수 있기는 하다.  
하지만 배열의 요소들에 삭제, 추가, 수정이 일어나면 i로만 적어둔 값에 문제가 생긴다.  
앞서 말했듯 **key는 고유한 값을 갖도록** 해야한다. 그런데 key={i]로 두면 아래와 같은 문제가 생길 수 있다.
```JS
// '바나나' 삭제 전
v = [{ fruit: '사과',   taste : '달다'}, 
     { fruit: '바나나', taste : '맛있다' }, 
     { fruit: '포도',   taste : '시다'}
    ]

// '바나나' 삭제 후
v = [{ fruit: '사과',   taste : '달다'}, 
     { fruit: '포도',   taste : '시다'}
    ]
```
자, 위를 보면 index가 1인 '바나나'를 삭제했다. 
그러면 기존에 '포도'는 key가 2 였는데, '바나나'를 삭제하고 나니까 '포도'의 key가 1이 되어 버렸다.  
이렇게 되면 **key는 고유한 값을 갖도록** 해야 한다는 성질에 문제가 생길 수 있다.  
'포도'가 아니라 '바나나'라고 오해할 수 있기 때문이다.  
  
## 왜 이렇게 지독하게 "key는 고유한 값을 갖도록" 해야하나?
key는 react가 각각 고유한 태그인지 구별할 수 있도록 하기위해 추가하는 것이다. react가 성능 최적화를 자체적으로 할 때 이 key 값을 보고 판단을 하는데, 저렇게 index로 key를 줘버리면 뭐가 바뀌고 뭐가 어떤지 알아차릴 수 없게 되기 때문이다.  
> react에서 key를 기준으로 엘리먼트를 추가하거나 수정 삭제 판단하기 때문에 배열의 순서가 바뀌면 문제가 생긴다.  
  
## 그래서 key 어떻게 해야하냐?
정답은 없다. 그냥 상황에 맞게 창의적으로, 적절히 고유한 값을 갖도록 설정해주면 된다.  
```
return (
  <li key={v.fruit+v.taste}><b>{v.fruit}</b>는 {v.taste}</li>
);
```
강의에서는 위와 같이 key를 설정했다.  
  
# 3-4. 컴포넌트 분리와 props
현재 render() 부분 전체를 보자
```js
render() {
  return (
      <>
          <h1>{this.state.result}</h1>
          <form onSubmit={this.onSubmitForm}>
              <input maxLength={4} value={this.state.value} onChange={this.onChangeInput} />
          </form>
          <div>시도: {this.state.tries.length}</div>
          <ul>
          {[
              { fruit: '사과',   taste : '달다'}, 
              { fruit: '바나나', taste : '맛있다' }, 
              { fruit: '포도',   taste : '시다'}, 
              { fruit: '귤',     taste : '시다'}, 
              { fruit: '수박',   taste : '맛없다'}, 
              { fruit: '메론',   taste : '너무달다'}
              ].map( (v) => {
                  return (
                  <li><b>{v.fruit}</b>는 {v.taste}</li>
                  );
              })}
          </ul>
      </>
  );
}
```
보기싫게 코드가 길어진다. 이제 이 코드를 컴포넌트 분리로 다듬어 보자.  

## 컴포넌트 따로 빼는 이유?
1. 가독성 좋다
2. 재사용성 좋다
3. 성능 최적화 할 때 좋다

## 1. 배열 위로 빼주기
```JS
fruits = [
        { fruit: '사과',   taste : '달다'}, 
        { fruit: '바나나', taste : '맛있다' }, 
        { fruit: '포도',   taste : '시다'}, 
        { fruit: '귤',     taste : '시다'}, 
        { fruit: '수박',   taste : '맛없다'}, 
        { fruit: '메론',   taste : '너무달다'}
    ];
    
...

{this.fruits.map( (v) => {
    return (
    <li key={v.fruit+v.taste}><b>{v.fruit}</b>는 {v.taste}</li>
    );
})}
```
강의에서는 위와 같이 fruits를 위에 생성해주고, this.fruits로 해줬다.  
  
## 2. return 태그 부분 다른 파일로 빼주기
```JS
return (
  <li key={v.fruit+v.taste}>
      <b>{v.fruit}</b>
      <div>컨텐츠1</div>
      <div>컨텐츠2</div>
      <div>컨텐츠3</div>
      <div>컨텐츠4</div>
  </li>
);
```
실무에서는 이런식으로 fruit 하나 아래에도 엄청 길게 코드가 뻗어나갈 수 있다.  
따라서 컴포넌트를 다른 파일로 해서 빼주도록 하겠다.
```JS
import Try from './Try';

...

<ul>
  {this.fruits.map( (v, i) => {
    return (
        <Try />
    );
  })}
</ul>
```
근데 이렇게 하면, Try라고 다른 파일을 만들고 거기다가 태그를 빼준다고 치자.  
그러면 Try 파일에다가는 어떻게 (v, i)를 전달해 줄 것인가? 라는 문제가 생긴다.
![image](https://user-images.githubusercontent.com/101965836/162348932-119fd42b-478f-44b3-86a8-09fc46900e2e.png)   
이렇게 " 'v'가 뭔데? " 라고 에러를 뱉어준다.  
따라서 (v, i)를 전달해줄 방법이 필요하고, 그게 바로 <strong>props</strong>다
```JS
<ul>
    {this.fruits.map( (v, i) => {
        return (
            <Try value={v} index={i} />
        );
    })}
</ul>
```
그러고 나서 이제 Try.jsx 파일을 만들고 아래와 같이 코드를 적어준다.  
```js
import React, { Component } from 'react';

class Try extends Component {
    render() {
        return (
            <li>
                <b>{this.props.value.fruit}</b> - {this.props.index}
                <div>컨텐츠1</div>
                <div>컨텐츠2</div>
                <div>컨텐츠3</div>
                <div>컨텐츠4</div>
            </li>
        )
    }
}

export default Try;
```

이렇게하면 잘 동작하는 모습을 볼 수 있다.  
  
# 3-5. 주석과 메서드 바인딩
## 주석
JS에서 블록 주석은 /* \*/로 썼었다.  
react에서 주석은 {/* \*/}로 써야한다  


## 메서드 바인딩
앞서 
``` JS
class NumberBaseball extends Component {
    state = {
        result: '',
        value: '',
        answer: getNumbers(),
        tries: "",
    };

    onSubmitForm = () => {
        return(
            '1'
        );
    };

    onChangeInput = () => {
        return(
            '1'
        );
    };
}
```

이런 식으로 class 에서 메서드는 전부 화살표 함수만 써줘야 했었다. 
```
  onSubmitForm(e) {
    e.preventDefault();
    console.log(this.state.value);
  };
```
이런 식으로 하면 this가 class를 가리키는게 아니라 전역객체(window)를 가리키게 되어 에러가 난다.  
따라서 이렇게 쓸려면 메서드 바인딩을 해줘야 한다.  
```
class NumberBaseball extends Component {
  constructor(props){
    super(props);
    this.state = { ... };
    this.onSubmitForm = this.onSubmitForm.bind(this);
    this.onChangeInput = this.onChangeInput.bind(this);
  }
  
  ...
```
이렇게 해줘야지만 this를 쓸 수 있다.    
근데 이게 참 문법이 이상해보여서 잘 안쓰게 된다.  
대신 화살표 함수를 쓰며, 화살표 함수가 bind(this)를 자동으로 해준다고 보면 된다.  
또 bind 안써도 되게 되면서 state도 밖으로 꺼낼 수 있게 되고,  
그와 동시에 constructor(props){ super(props) }도 사라지게 됐다.  
다만 옛날 코드도 봐야 할 일이 있을 수 있으니 알아둬야 한다.  
  
  
  
# 3-6. 숫자야구 만들기

## function getNumbers() 구현
```JS
function getNumbers() { // 숫자 네 개를 겹치지 않고 랜덤하게 뽑는 함수 
    const candidate = [1,2,3,4,5,6,7,8,9];
    const array = [];
    for (let i = 0; i<4; i+= 1){
        const chosen = candidate.splice(Math.floor(Math.random()*(9-i)),1)[0];
        // splice에 대해 설명. myArray.splice(a,b); 라고 하면 myArray에서 a번째 인덱스에서 b개 만큼 먼저 선택이 된다. 
        // myArray에서는 선택 된 요소들이 삭제가 되고, 메소드의 return은 삭제된 요소들이 된다.
        // 예를 들어 myArray = [1,2,3,4]; 라고 할 때 const chosen = myArray.splice(1,1); 이라고 하면, 1번째 인덱스인 2가 선택된다. 
        // 그 다음, 2는 myArray에서 삭제되어서 myArray = [1,3,4]가 된다. 그리고 return 이 삭제된 [2]이므로, chosen = [2] 가 된다.
        // 여기서 배열로 return이 되므로, 0번째 인덱스 요소의 값만 얻을려면 const chosen = myArray.splice(1,1)[0]; 라고 하면 된다. 
        array.push(chosen);
    }
    return array;
}
```

## .join() 매서드
```js
onSubmitForm = (e) => {
    e.preventDefault();
    if (this.state.value === this.state.answer.join('')){
    
    } else {

    }
};
```
state.value는 "1234" 같은 string으로 들어온다. 그런데 answer.join('')은 getNumbers()로 얻은거라서 \[1,2,3,4] 같은 배열로 되어 있다. 따라서 \[1,2,3,4]를 "1234"로 바꿔주기 위해서 .join()을 쓴다. (그런데 그냥 getnumbers 함수에서 join하고 넘기면 되지않나?)  
  
  
## react에서 push로 배열을 바꾸면 render가 실행되지 않음
결론 : arr1 !== arr2 이여야지만 render 함수가 새로 실행된다. 근데 arr1 에다가 arr1.push(...)로 새 요소를 추가하면, arr1 === arr1로 배열 주소는 같기 때문에 render 함수가 새로 실행되지 않는다. 따라서 아래의 방법을 써야한다.
```js
const arr1 = [1,2,3,4]
const arr2 = [...arr1, 5] // [1,2,3,4,5]
// arr1 랑 arr2는 다른 값이 되게 된다 
```  

## 수정사항 추가
홈런 부분에서 tries:\[...this.state.tries , ] 를 썼는데, 이렇게 하면 안된다. (3-8강 2분20초 쯤 설명 추가)   
this.state.tries인데 옛날 값으로 현재 값을 설정할때는 setState를 함수형으로 바꾸고 (prevState) 받아와서 prevState.tries로 써야한다.    
```
if (this.state.value === this.state.answer.join('')){
this.setState((prevState) => {
    return {
        result: '홈런!',
        tries: [...prevState.tries, { try: this.state.value, result:'홈런!'}], // push 쓰면 안됨. 안되는 이유는 TIL 참고
    }
})
```
**옛날 state로 현재 state를 만들 때는, 함수형으로 하고 prevState 사용!**  


## 추가. 지금까지 코드 전문  
  
NumberBaseball.jsx  
```js
import React, { useState } from 'react';
import Try from './Try';

function getNumbers() { // 숫자 네 개를 겹치지 않고 랜덤하게 뽑는 함수 
    const candidate = [1,2,3,4,5,6,7,8,9];
    const array = [];
    for (let i = 0; i<4; i+= 1){
        const chosen = candidate.splice(Math.floor(Math.random()*(9-i)),1)[0];
        // splice에 대해 설명. myArray.splice(a,b); 라고 하면 myArray에서 a번째 인덱스에서 b개 만큼 먼저 선택이 된다. 
        // myArray에서는 선택 된 요소들이 삭제가 되고, 메소드의 return은 삭제된 요소들이 된다.
        // 예를 들어 myArray = [1,2,3,4]; 라고 할 때 const chosen = myArray.splice(1,1); 이라고 하면, 1번째 인덱스인 2가 선택된다. 
        // 그 다음, 2는 myArray에서 삭제되어서 myArray = [1,3,4]가 된다. 그리고 return 이 삭제된 [2]이므로, chosen = [2] 가 된다.
        // 여기서 배열로 return이 되므로, 0번째 인덱스 요소의 값만 얻을려면 const chosen = myArray.splice(1,1)[0]; 라고 하면 된다. 
        array.push(chosen);
    }
    return array;
}


const NumberBaseball = () => {
    const [result, setResult] = useState('');
    const [value, setValue] = useState('');
    const [answer, setAnswer] = useState(getNumbers());
    const [tries, setTries] = useState([]);

    const onSubmitForm = (e) => {
        e.preventDefault();
        if (value === answer.join('')){
            setResult('홈런!');
            setTries( (prevTries) => {
                return ( [...prevTries, { try: value, result:'홈런!'} ] )
                }
            );
            alert(`홈런! 정답은 ${answer.join("")}입니다!!`);
            alert('게임을 다시 시작합니다!');
            setValue('');
            setAnswer(getNumbers());
            setTries([]);
            
        } else { // 답 틀렸으면
            const answerArray = value.split('').map((v) => parseInt(v)); // string을 한 글자씩 쪼갠다음, int형으로 변환해서 array로 저장
            let strike = 0;
            let ball = 0;
            if (tries.length >= 9){
                setResult(
                    `10번 넘게 틀려서 실패! 답은 ${answer.join(',')}였습니다!`
                );
                alert('게임을 다시 시작합니다!');
                setValue('');
                setAnswer(getNumbers());
                setTries([]);
            } else {
                for ( let i = 0; i < 4; i += 1){
                    if (answerArray[i] === answer[i]){
                        strike += 1;
                    } else if (answer.includes(answerArray[i])){
                        ball += 1;
                    }
                };
                setTries( (prevTries) => {
                    return (
                        [...prevTries, { try: value, result: `${strike} 스트라이크, ${ball} 볼입니다`}]
                    );
                });
                setValue('');
            }
        }
    };

    const onChangeInput = (e) => {
        setValue(e.target.value);
    };

    return (
        <>
            <h1>{result}</h1>
            <form onSubmit={onSubmitForm}>
                <input maxLength={4} value={value} onChange={onChangeInput} />
            </form>
            <div>시도: {tries.length}</div>
            <div>${answer}</div>
            <ul>
                {tries.map( (v, i) => {
                    return (
                        <Try key={`${i+1}차 시도`} tryInfo={v} index={i} />
                    );
                })}
            </ul>
        </>
    );
}


export default NumberBaseball;
```   

Try.jsx
```js
import React, { Component } from 'react';

class Try extends Component {
    render() {
        return (
            <li>
                <div>{this.props.tryInfo.try}</div>
                <div>{this.props.tryInfo.result}</div>
            </li>
        )
    }
}

export default Try;  
```
  
#### 3-7 Q&A
Q : getNumbers() class 밖에 빼는 이유는 뭔가요?
A : this를 안 쓰는 경우에는 그냥 밖으로 빼도 된다. 그냥 다른 곳에서 재사용이 가능하기 때문이다.  
   
   
# 3-8 hooks로 바꾸기
   
   
## Try.jsx 바꾸기
구조분해 사용하는 방법이랑, props 쓰는 방법 2가지가 있다.  
#### (1) 구조분해
```js
import React, { Component } from 'react';

const Try = ({ tryInfo }) => {
    return (
        <li>
            <div>{tryInfo.try}</div>
            <div>{tryInfo.result}</div>
        </li>
    );
}

export default Try;
```
#### (2) props
```js
import React, { Component } from 'react';

const Try = (props) => {
    return (
        <li>
            <div>{props.tryInfo.try}</div>
            <div>{props.tryInfo.result}</div>
        </li>
    );
}

export default Try;
```
  
보통은 구조분해를 많이들 쓴다고 한다. props는 부모요소에서 뭐 상속되고 이런 부분이 좀 골치아플 수 있다고 한다.  
  
  
## Uncaught ReferenceError: $RefreshReg$ is not defined 에러 해결 
![image](https://user-images.githubusercontent.com/101965836/162380263-616b16ba-c466-4c48-b43d-30f278b81ae8.png)  

```
Uncaught ReferenceError: $RefreshReg$ is not defined
    at eval (Try.jsx:19:1)
    at Module../Try.jsx (app.js:41:1)
    at __webpack_require__ (app.js:384:33)
    at fn (app.js:596:21)
    at eval (NumberBaseball.jsx:7:62)
    at Module../NumberBaseball.jsx (app.js:30:1)
    at __webpack_require__ (app.js:384:33)
    at fn (app.js:596:21)
    at eval (client.jsx:5:73)
    at Module../client.jsx (app.js:52:1)
```
class로 구현 할 때 까지는 잘 됐는데 갑자기 hook로 바꾸니 이런 에러가 떴다. 일단 에러 자체가 webpack.config.js 에서 설정할 때 생기는 문제인 것 같다. RefreshReg가 앞서 핫 리로딩 설정할 때 건드렸던 애랑 관련있는 것 같아서 그 부분에서 이것저것 건드려 봤다.
  
문제는 webpack.config.js에서 plugins:랑 관련 있었다.  
plugins가 들어가는 부분은 아래와 같다.  
```JS
module: {
    rules: [{
        test: /\.jsx?./,
        loader: 'babel-loader',
        options:{
            presets: ['@babel/preset-env', '@babel/preset-react'],
            plugins: [
                'react-refresh/babel',
            ]
        },
    }],
},

plugins:[
    new RefreshWebpackPlugin(),

],
plugins:[
    new HtmlWebpackPlugin({
    title: 'Hot Module Replacement',
  }),
],
```
여기서 대부분 검색 결과는 bebel-loader에 있는 plugins에 대해서 이야기 하는데, 나는 그게 문제가 아니라 바깥에 있는 plugins들이 문제였다. 이것저것 덕지덕지 검색해서 갖다 붙이다 보니 plugins가 2개 따로 들어가 있었다. 이걸 합쳐서
```js
plugins:[
    new RefreshWebpackPlugin(),
    new HtmlWebpackPlugin({
        title: 'Hot Module Replacement',
      }),
],
```
이렇게 만들어 주니 잘 작동했다. 굳.  
  
  
  
# 3-9 React Devtools
렌더링이 자주 일어나서 성능이 안좋아지는 문제를 찾는 방법.   
chrome extension에서 React Developer tools를 다운받음. 그 다음 검사 창에서 profiler에서 보면 record 해서 render에 걸린 시간을 볼 수 있다.   

## React Developer tools 빨간 아이콘은 무엇?
![image](https://user-images.githubusercontent.com/101965836/162384163-57ad1620-2bf5-43aa-a72c-899dd2b5089c.png)  
개발 모드라는 뜻. 

# 3-10 shouldComponentUpdate

shouldComponentUpdate는 class로 컴포넌트 만들 때 쓰는애다. hooks일때는 react.memo 참고.  
  
![image](https://user-images.githubusercontent.com/101965836/162385034-01331bad-0b41-4868-902b-ab32bd6680c4.png)   
이 옵션 체크하고 보면  
![image](https://user-images.githubusercontent.com/101965836/162385097-3c3903fe-828e-48e8-84db-c2075f2ceab6.png)  
그냥 사용자는 input에 숫자만 입력할 뿐인데, 아래에 쓸데없이 tries도 render 되는 문제가 생긴다.   
즉, 안바뀌어서 render 될 필요 없는데, 불필요하게 자원을 쓰고 있는 것이다.  

## 왜?
react는 setState만 호출되어도 render가 일어나게 된다. 

## 어떻게 최적화?
shouldComponentUpdate()를 사용하면 어떤 경우에 render할지 세세하게 설정할 수 있다.  

# 3-11 PureComponent와 React.memo

## (1) PureComponent
purecomponent는 class로 컴포넌트 만들 때 쓰는애다.
PureComponent란 shouldComponentUpdate를 알아서 구현한 컴포넌트이다.  
다만 PureComponent도 자료구조가 복잡해지면 못알아 차릴 수 있다. 예를 들어
``` 
state = {
  counter: 0,
  string: 'hello',
  number: 1,
  boolean: true,
  object: { a: 'b', c: 'd' },
  array: [5, 3, 6],
}
```
이렇게 간단하게 만들어야 purecomponent가 잘 알아차리지
``` 
state = {
  dontdothis : [{dont:[3]}]
}
```
이렇게 해버리면 못 알아차릴 수 있어서 안좋다.  

### 왜 못알아차리는가?
```
const array = this.state.array;
array.push(1);
this.setState(array:array);
```
이렇게 하면 array 주소 자체는 그대로니까, pure가 '이건 그대로구나' 하고 업데이트 하지 않게 된다.  
근데 push를 했으니 1이 추가되었지 않는가? 이래서 push를 쓰면 안된다.  
마찬가지로 state가 복잡해지면, 저런식으로 주소가 같아지면서 뭐 꼬일 수 있나보다.  

### "state에 객체 구조 안쓰는게 좋다" 왜?
{a:1} 에서 setState({a:1})을 하면 새로 렌더링 하게 된다. 따라서 state에서는 객체 구조를 안쓰는게 좋다.  


### 추가 : 무조건 pure로?  
상황에 맞춰서 쓰면 된다. 복잡해지면 purecomponent가 원하는대로 동작하지 않을 수 있는 등 다양한 시나리오가 있다. 그러므로 경우에 따라서 component를 쓰는 것도 좋은 선택이 될 수 있다.   
1. 최우선적으로 state들이 복잡해지지 않도록 component들을 잘게 쪼개려 노력한다  
2. 불가피하게 복잡해지면 component로 쓰고 shouldComponentUpdate로 쓴다  
3. 세부적으로 render를 조절할 상황이 오면 component를 쓰자  
  
## (2) React.memo
hook는 shouldComponentUpdate도 없고 PureComponent도 없다. 그럼 뭘 써야할까? hook에서는 memo를 쓴다.  
Try.jsx 예시를 보자  
```js
import React, { Component } from 'react';

const Try = ({ tryInfo }) => {
    return (
        <li>
            <div>{tryInfo.try}</div>
            <div>{tryInfo.result}</div>
        </li>
    );
}

export default Try;
```
이 코드를
```js
import React, { Component, memo } from 'react';

const Try = memo(
    ({ tryInfo }) => {
    return (
        <li>
            <div>{tryInfo.try}</div>
            <div>{tryInfo.result}</div>
        </li>
    );
});

export default Try;
```

이렇게 바꿔주면 된다.   
  
  
# 3-12 React.createRef
앞서 검색해서 적용해본적 있던 내용이다. 이 md문서에서 ( createRef사용법 ) 이라고 찾으면 된다.  
  
  
# 3-13 props와 state 연결하기
props를 자식 컴포넌트에서 바꾸는건 좋지 못하다. 다만 바꿔야 한다면 state로 자식에다가 따로 만들어 놓고(복사해두는 느낌), state로 바꿔주는게 좋다.  

## props를 바꾸면 안되는 이유
부모가 자식한테 props를 물려주는거니까, 자식이 props를 바꿔버리면 부모의 props도 바뀌어야 한다. 그런데 이렇게 되면 의도하지 않는 방식으로 바뀔 수 있다. 따라서 state로 복사해서 값을 갖고 setState로 바꾸는게 좋다.  
  
  

