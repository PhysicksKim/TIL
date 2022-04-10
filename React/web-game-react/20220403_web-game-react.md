# 세팅 방법 정리
아래의 내용은 본 강좌에서 가위바위보게임 (RPSgame)을 만들 때 정리했음. 상세 코드 예시 부분들 모두 RPSgame에 따라서 제작한 코드임. 적절히 상황에 따라서 수정해서 사용.   
  
## 0. create-react-app
```  
npx create-react-app (앱이름)
cd my-app
npm start
```  
이 때 앱 이름은 소문자만 써야한다.   
  
## 1. 기본 패키지들 설치
```
npm i react react-dom
npm i -D webpack webpack-cli
npm i -D @babel/core @babel/preset-env @babel/preset-react babel-loader
npm i react-refresh @pmmmwh/react-refresh-webpack-plugin -D
npm i -D webpack-dev-server
npm install --save-dev html-webpack-plugin  
```
  
## 2. create-react-app으로 생성된 불필요한 파일 삭제 및 필요한 파일 생성
#### 2-1. src, public폴더 삭제
#### 2-2. 프로젝트 디렉토리에 곧바로 아래 파일 생성
```
index.html
(프로젝트구현).jsx 
client.jsx
webpack.config.js
```
![image](https://user-images.githubusercontent.com/101965836/162555318-15fd4a2e-f419-4323-bd94-97bd9e42f056.png)   
(위 파일들 중에서 파일 이름이 강제되는건 webpack.config.js 밖에 없음. 나머지는 바꾸고 싶으면 알아서 설정 건드려서 바꿀 수 있음)  
(1) index.html이 라우팅 되는 html 문서 파일.  
(2) (프로젝트구현).jsx는 메인이 되는 프로젝트 컴포넌트가 구현되는 코드 파일. 파일명은 자기 프로젝트 이름에 맞게 알아서 설정. ex. RPSgame.jsx  
(3) client.jsx는 (프로젝트구현).jsx 에서 만든 컴포넌트를 갖고오고, 또 html에서 root 부분 DOM을 갖고와서, HTML root DOM에다가 구현한 컴포넌트를 집어넣는(render) 역할을 하는 코드 파일.  
(4) webpack.config.js는 webpack 설정을 위한 파일. 사실은 webpack 빌드 이후 최종적으로는 client.jsx가 html에다가 컴포넌트를 넣는게 아님. 아래에서 할 webpack 설정에 따라서, webpack이 지금까지 작성한 .js .jsx 파일들을 모아서 한방에 정리하고, 정리한 파일을 ./dist/app.js에 넣어서 app.js를 HTML이 실행시킴. 그러면 app.js가 root에서 dom 따와서 우리가 작성한 component를 넣어주는거임. 물론 핵심 코드들도 다 app.js에 한방에 들어가있음.  

## 3. 각 파일 내용 작성
#### 3-1 index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>가위바위보</title>
</head>
<body>
    <div id="root"></div>
    <script src="./dist/app.js"></script>
</body>
</html>
```
앞서 설명했듯, id="root"으로 해줘서 root을 만들어 놔야지 DOM을 얻어서 컴포넌트를 집어넣을 수 있음.  
script src="./dist/app.js" 부분도 앞에서 말했듯 webpack이 한방에 js 파일들을 취합해서 만들어 주는 파일이 app.js가 되니까 app.js를 실행시키는 것임.   
  
#### 3-2 (프로젝트구현).jsx  ex.RPSgame.jsx
```js
import React, { Component } from 'react';

class RPSgame extends Component{
    render(){
        return <h1>hello RPSgame</h1>
    }
}

export default RPSgame;
```

#### 3-3 client.jsx
```js
import React from 'react';
import { createRoot } from 'react-dom/client';
import RPSgame from './RPSgame';

const container = document.querySelector('#root');
const root = createRoot(container);

root.render(<RPSgame />);
```

#### 3-4 webpack.config.js
```js
const path = require('path');
const RefreshWebpackPlugin = require('@pmmmwh/react-refresh-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');

// 실서비스 : process.env.NODE_ENV = 'production'; 추가

module.exports = {
    name: 'rpsgame',
    mode: 'development', // 실서비스 : production
    devtool: 'eval', // 실서비스 : hidden-source-map
    resolve: {
        extensions: ['.js','.jsx'], 
    },
    entry: {
        app: ['./client'],
    }, 
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
        new HtmlWebpackPlugin({
            title: 'Hot Module Replacement',
        }),
    ],
    
    output: {
        path: path.join(__dirname, 'dist'), 
        filename: 'app.js',
    },
    devServer: {
        devMiddleware: {
            publicPath: '/dist', // webpack의 output 파일이 있는 경로
        },
        static: {
            directory: path.resolve(__dirname), // index.html이 존재하는 경로
        },
        hot: true,
    },
};
```

#### 3-5 package.json 파일 수정
```json
"scripts": {
  "dev":"webpack serve --env development",
  
  
  }
```  
  
핫 리로딩은 제대로 동작하지 않는 듯 한데 일단 지금은 차치해두자.  

---

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
  
  

---
# 4 반응속도 체크
# 4-1 React 조건문
render(){ return () } 구문에서 return 안에는 for이랑 if를 못써서 다른 방식으로 해야한다. 앞서서 for 대신 map을 쓰는 방법을 봤다.  
그러면 조건문이 필요할땐 어떻게 해야할까?

## 조건문이 필요한 예시
반응속도 체크는 
![image](https://user-images.githubusercontent.com/101965836/162441734-8032c4cc-23cc-499f-927b-240968e9b413.png)  
![image](https://user-images.githubusercontent.com/101965836/162441794-a8cf1375-b2fc-4540-8873-be755b943e04.png)  
이런식으로 빨간색이던 화면이 초록색으로 바뀌면 클릭해서 반응속도를 체크하는 게임이다.  
  
여기서 우리가 구성할 render() 구문을 보면  
```js
render(){
    return (
        <>
        <div id="screen" className={this.state.state} onClick={this.onClickScreen}>
        {this.state.message}
        </div>
        <div>평균 시간: {this.state.result.reduce((a,c) => a + c) / this.state.result.length}ms</div>
        </>
    );
};
```
이렇게 된다. 여기서 " \<div>평균 시간: " 부분에서 reduce로 구현된 부분이 배열의 각요소를 더해서 평균시간을 내는 부분이다. 근데 이 때 배열의 길이가 0인, 즉 시작하자마자 빈 배열인 상태라면 이를 표시하고싶지 않을 수 있다.  
그러면 조건에 따라 return을 달리 하고 싶다면 어떻게 해야할까?  
  
## (1) 삼항연산자 
```js
render(){
    return (
        <>
        <div id="screen" className={this.state.state} onClick={this.onClickScreen}>
        {this.state.message}
        </div>

        { this.state.result.length === 0 ? null
        : <div>평균 시간: {this.state.result.reduce((a,c) => a + c) / this.state.result.length}ms
        </div>
        }
        </>
    );
};
```
이런식으로 삼항연산자를 사용해서 표현할 수 있다.  
이 때 null, false, undefined는 jsx에서 태그 없음을 의미한다.  

## (2) Bool연산자
삼항연산자와 마찬가지로 { } 안에서 \&& 라던가 \| 같은 bool 연산자를 사용해서 조건문을 만들 수 있다.  
  
## (3) 따로 밖으로 빼기
```js
renderAverage = () => {
    const { result } = this.state;
    return result.length === 0 
    ? null
    : <div>평균 시간: {result.reduce((a,c) => a + c) / result.length}ms
    </div>
};

render(){
    const { state, message } = this.state;
    return (
        <>
        <div id="screen" className={state} onClick={this.onClickScreen}>
        {message}
        </div>
        {this.renderAverage()}
        </>
    );
};
```
비교적 깔끔하게 코드 정리까지 했다. 이렇게 하는게 훨씬 좋아보인다.  

(이하 뇌피셜)
사실 앞의 (1) (2)는 장기적으로 보면 크게 쓸모 없다고 생각한다. 더 복잡한 조건분기에는 따로 빼는게 적합하다고 생각한다. 따라서 장기적으로 보면 따로 빼는게 가독성도 좋고 재생산성부터 유지보수까지 편안해지는 방식으로 보인다.  


# 4-2 setTimeout 넣어 반응속도 체크
setTimeout으로 random초 뒤에 클릭 화면으로 바뀌도록 하면 된다. 화면 바뀌도록 하는것 자체는 어렵지 않다. setState를 setTimeout 안에 넣어주면 되니까. 근데 여기서 만약 화면이 바뀌기 전에 클릭했다면, 이미 큐에 들어간 setTimeout을 없애줘야 하지 않겠는가. 여기서 clearTimeout 이라는 메소드를 사용한다. 
```js
timeout;

onClickScreen = () => 
  if ( 대기 ) {
    this.timeout = setTimeout( () => { setState({...})});
  } else if ( 성급하게 누름 ) {
    clearTimeout(this.timeout);
  } else if ( ... ) { ... }
}
```
이런식으로 구성이 된다.

# 4-4 Hooks로 전환
거의 다 같은데, 앞서 본 timeout이랑 startTime endTime 부분이 조금 달라진다.  
## Class에서는 이렇게 했었다
```js
timeout;
startTime;
endTime;

onClickScreen = () => 
  if ( 대기 ) {
    this.timeout = setTimeout( () => { 
      setState({...});
      this.startTime = new Date();
      }, 2~3초 랜덤 )
    );
  } else if ( 성급하게 누름 ) {
    clearTimeout(this.timeout);
    ...
  } else if ( 제대로 누름 ) { 
    this.endTime = new Date();
    ... 
    
    setState( (prevState) => { return(
      ...
      Result : [...prevState, endTime - startTime];
      ...
      );
    });
  }
}

...
```

## Hooks에서는 이렇게 해야 한다
```js
// timeout;
// startTime;
// endTime;

const timeout = useRef(null);
const startTime = useRef();
const endTime = useRef();

const onClickScreen = () => {
  if ( 대기 ) {
    timeout.current = setTimeout( () => { 
      ...
      startTime.current = new Date();
      }, 2~3초 랜덤 )
    );
  } else if ( 성급하게 누름 ) {
    clearTimeout(timeout.current);
    ...
  } else if ( 제대로 누름 ) { 
    endTime.current = new Date();
    ...
    setResult((prevState) => { return [...prevState, endTime.current - startTime.current]} ); 
  }
};
```
일단 ref로 안하고 state로 해버리면, state는 값이 바뀌면 랜더링이 다시 되기 때문에 적절하지 못하다.   
useRef는 이전에 DOM요소를 갖고와서 바꿀 때 사용했었는데, 또 다른 사용법으로 값이 바뀌기는 하지만 render를 시키고 싶지는 않을 때 사용한다.  

# 4-5 return 내부에 for과 if 쓰기
앞서 이야기 했듯, return 안에 if와 for문을 못 쓴다. 하지만 어거지로 쓸 수 있기는 하다.  
```
return ( 
  {(() => {
    if (result.length === 0) {
      return null;
    } else {
      return <>
          <div>평균 시간: {result.reduce((a,c) => a + c) / result.length}ms</div>
          <button onClick={reset}>리셋</button>
        </>
    }
  })()}
);
```
이렇게 {} 안에다가 ()=>{...}로 함수를 만들고 이걸 다시 ( ()=>{...} )() 괄호로 묶은 다음 바로 () 넣어서 즉시 실행되도록 만들면 된다.  
근데 이러면 너무 코드가 지저분해지니 그냥 기존 방식대로 따로 빼는게 좋다.  
  
---
  
  
# 5 가위바위보 게임
   
# 5-1 리액트 라이프사이클 소개
컴포넌트가 클라이언트.jsx에 불러와서 랜더링되고 DOM에 갖다 붙는 순간이 있는데, 그 때 특정한 동작을 하도록 할 수 있다.   
componentDidMount() { }   
랜더가 처음 실행되고 성공적으로 실행 됐다면 저게 실행된다. 그 다음부터 setState같은거로 rerender가 실행되면 componentDidMount가 실행되지 않는다.   
마찬가지로 컴포넌트가 제거되는 경우에는 
componentWillUnmount 가 실행된다.  
```js
componentDidMount() {  // 컴포넌트가 첫 렌더링 된 후
  ...
}
componentDidUpdate() {  // 리렌더링 후
  ...
}
componentWillUnmount() {  // 컴포넌트가 제거되기 직전
  ...
}

// 라이프사이클 
// 클래스경우
// constructor -> render -> ref -> componentDidMount
// -> (setState/props 바뀔 때 -> shouldComponentUpdate -> render -> componentDidUpdate)
// 부모가 나를 없앴을 때 -> componentWillUnmount -> 소멸
```

## 갑자기 이게 왜? 
다음 강의에서 그 이유가 나온다.
  
  
# 5-2 setInterval과 라이프사이클 연동하기
```js
// interval;
    
    componentDidMount() {  // 컴포넌트가 첫 렌더링 된 후, 여기에 비동기 요청을 많이 함
        // ex. this.interval = setInterval( () => {} ) 했으면 나중에 필요 없을 때 이걸 없애줘야함
    }

    componentDidUpdate() {  // 리렌더링 후

    }

    componentWillUnmount() {  // 컴포넌트가 제거되기 직전, 비동기 요청 정리를 많이 함
        // clearInterval(this.interval);
    }
```
이렇게 Interval 만들었으면 필요 없어졌을 때 없애줘야함. 계속 남아있으면 메모리 누수 문제가 발생하기 때문임.  

## 클로저 문제
```js
interval;

componentDidMount() {  // 컴포넌트가 첫 렌더링 된 후, 여기에 비동기 요청을 많이 함
  const {imgCoord} = this.state;
  this.interval = setInterval( () => {
    if (imgCoord === rspCoords.바위){
        this.setState({
            imgCoord: rspCoords.가위,
        });
    } else if (imgCoord === rspCoords.가위){
        this.setState({
            imgCoord: rspCoords.보,
        });
    } else if ( imgCoord === rspCoords.보){
        this.setState({
            imgCoord: rspCoords.바위,
        });
    }
  }, 1000);
}
```
이렇게 하면 imgCoord는 -142px로 계속 반복된다. 왜냐하면 비동기의 클로저 문제 때문이다.  

```js
interval;

componentDidMount() {  // 컴포넌트가 첫 렌더링 된 후, 여기에 비동기 요청을 많이 함
  this.interval = setInterval( () => {
    const {imgCoord} = this.state;    // 여기를 바꿔줘야 한다
    if (imgCoord === rspCoords.바위){
        this.setState({
            imgCoord: rspCoords.가위,
        });
    } else if (imgCoord === rspCoords.가위){
        this.setState({
            imgCoord: rspCoords.보,
        });
    } else if ( imgCoord === rspCoords.보){
        this.setState({
            imgCoord: rspCoords.바위,
        });
    }
  }, 1000);
}
``` 

# 5-4 고차 함수와 QNA

```
onClickBtn = (choice) => { 
  ...
};

...

<div>
    <button id="rock" className='btn' onClick={()=>this.onClickBtn('바위')}>바위</button>
    <button id="scissor" className='btn' onClick={()=>this.onClickBtn('가위')}>가위</button>
    <button id="paper" className='btn' onClick={()=>this.onClickBtn('보')}>보</button>
</div>
```
여기서 보면  onClick={()=>this.onClickBtn('바위')} 이 부분에서 앞에 ()=> 를 위로 뺄 수 있다.

```js
onClickBtn = (choice) => ()=> { 
  ...
};
 
...
 
<div>
    <button id="rock" className='btn' onClick={this.onClickBtn('바위')}>바위</button>
    <button id="scissor" className='btn' onClick={this.onClickBtn('가위')}>가위</button>
    <button id="paper" className='btn' onClick={this.onClickBtn('보')}>보</button>
</div>
```
이렇게 할 수 있으며 고위함수, 고차함수 라고 한다.  

# 5-5 Hooks와 useEffect
```
useEffect( () => { 
// componentDidMount, componentDidUpdate 역할 (1대1 대응은 아님) 둘을 하나로 합쳐둠
    interval.current = setInterval(changeHand,100);

    return () => { 
        // componentWillUnmount 역할 
        clearInterval(interval.current);
    }
}, 
[imgCoord] // 얘가 그 클로저 문제 생겨서 멈췄을때 그거를 해결하는 역할
);
```

useEffect가 useEffect( callback, [] ) 형태로 구성되는데 첫 번째 callback 함수 집어넣는건 위의 주석으로 설명을 했다. 그런데 두 번째 [] 이부분이 좀 어렵다.  

(이하 내가 이해한대로 설명하는 뇌피셜)
자. useEffect( callback, [] ) 얘는 [] 안에 들어있는 애가 바뀔 때 마다 실행되는거다.   
(위 코드에서는 imgCoord 값이 바뀔 때 마다 useEffect에 넣은 callback 함수가 실행되는 것이다)   
[]를 빈칸으로 둘때는 setInterval이랑 clearInterval이 최초 랜더링 될 때 한번만 실행된다.   
그래서 changeHand가 딱 한번 실행되고 끝난다.  
그런데 \[imgCoord]로 두면 imgCoord 값을 주시하면서 imgCoord 값이 바뀌면 useEffect를 다시 실행한다.  
그런식으로 setInterval이 됐다가 clearInterval이 됐다가 이걸 계속 반복하게 된다.    
  
  
조금 이상해 보일 수 있는데.   
class로 할 때는 componentDidMount에다가 setInterval을 넣어서, 첫 랜더링 된 후 한번만 setInterval한다.  
그리고 컴포넌트가 제거되기 직전에 componentWillUnmount에다가 clearInterval로 제거한다.   
근데 hooks에서는 이걸 imgCoord가 제거될 때마다 계속 반복한다. 조금 기이한 모습이긴 하다.  
  
  

# 5-6 class와 hooks 라이프사이클 비교  
 
class에서 component~~ 하는 애들은 한번만 쓸 수 있고,  
hooks의 useEffect는 여러번 쓸 수 있다.  
class에서는 componentWillUnmount 하나에다가 조건문으로 왕창 때려박고 한곳에서 처리해야한다.  
그런데 hooks의 useEffect는 여러 개를 쓸 수 있고, 원하는 값에 따라서 update 하도록 넣을 수 있다.  




---

# 6. 로또 당첨 만들기

# 6-2. setTimeout 여러 번 사용하기
```js
for (let i = 0; i < winNumbers.length - 1; i++) { 
    // 여기서 let을 쓰면 i에 클로저 문제가 발생하지 않는다.
    timeouts.current[i] = setTimeout(() => {
        setWinBalls( (prevWinBalls) => [...prevWinBalls, winNumbers[i]])
    }, (i+1)*1000);
}
```
let을 쓰면 클로저 문제 발생하지 않음

# 6-5 useMemo와 useCallback
## useMemo
성능 최적화를 위해 useMemo를 사용한다. 
```js
const Lotto = () => {
  const [winNumbers, setWinNumbers] = useState(getWinNumbers());
  ...
```
![image](https://user-images.githubusercontent.com/101965836/162599075-e2243a8c-6ed2-4ab9-9e63-b93ee7f87be6.png)  
이렇게 하면, 랜더링 될 때마다 Lotto가 계속 재실행 되니까, getWinNumbers()도 계속 재실행 된다. 위의 스크린샷에서 볼 수 있듯, getwinnumbers가 불필요하게 여러번 실행(7번)된것을 볼 수 있다)  
  
  
따라서 성능 최적화를 위해 useMemo를 여기에 활용했다.  
[useMemo에 대한 설명 참고한 글](https://velog.io/@kysung95/%EC%A7%A4%EB%A7%89%EA%B8%80-useMemo)  
방식은 간단하다. 
```js
const 변수 = useMemo( callback, \[값들] );
```
useMemo가 함수의 return값을 기억하고 있으며, 뒤에 \[값들]이 바뀌기 전까지는 callback 함수는 재 실행되지 않는다. 또한 callback 함수의 return값은 '변수'에 저장된다.  

만약 빈 \[]로 놔두면 최초 한번만 실행되고 끝.  

## useCallback
마찬가지로 useMemo와 같이 성능 최적화를 위해 사용한다. 다만 useMemo는 안에 있는 함수의 return 값을 기억했다면, useCallback은 함수를 기억한다.  
예를 들어 const myfunction = useCallback( 함수 , \[값] ) 이라고 코드를 적었다 치자.  
그러면 계속 re-rendering 되어서 hooks 안이 계속 재선언 재선언 되면, 안에 있는 함수가 계속 새로 선언되는 문제가 발생한다. 여기서 만약 함수 선언에만 몇 초씩 걸리는 무거운 선언문이라면? 성능 문제가 발생하게 된다.  
따라서 이를 막기 위해서 useCallback으로 함수가 계속 재선언 되는것을 막을 수 있다. 함수 자체를 useCallback이 기억해두게 된다.  

### 함수마다 useCallback을 쓰면 이득? 
useCallback으로 감싸더라도 어지간한 함수는 다 정상 작동한다. 따라서 제대로 쓸 수 있으면 최대한 써주는게 좋을 수 있다.   
  
  
그런데 사용시 변하는 값을 적절히 추적할 수 있도록 주의해야한다.  
useCallback 안에서 state가 쓰이면, 바뀐 state 값을 출력하는게 아니라 처음에 값을 그대로 갖고 있게 된다.  
예를 들어보자.  
1초마다 number라는 state는 1씩 증가한다. 그런데 아래와 같이
```js
const [number, setNumber] = useState('1');

...

const showNumber = useCallback( () => {
  console.log(number);
}, [] );
```
이런식으로 짜여있으면 어떻게 될까?    
흔히 하는 오해대로 생각하면,  
" 음. number라는 변수(state)에 가서 값을 가져와서 console.log에 출력하니까 number가 증가하면 그에 맞춰서 state에서 값을 가져와서 출력하겠지? "  
라고 생각할 수 있다.  
하지만 이 때 number는 처음에 useCallback에 저장된 '1'을 갖고 계속 출력하게 된다.  
이는 참조형인 배열에서도 같은 문제가 발생한다. (강의 6-5 참고)    
    
   
따라서 이를 해결하기위해, 안에 state같은 변할만한 값이 들어가면 \[]안에다가 넣어줘야한다. 위의 예시의 경우 \[number]라고 넣어줘야 제대로 동작할 것이다.  
  
## 자식 component에다가 props에 함수를 넘길때 useCallback을 써야 한다  
말이 쫌 어렵다 차근히 설명해보자.  
#### (1) '자식 component에다가'
```js
const Lotto = () => {
  ...
  return (
      <>
          <div>당첨 숫자</div>
          <div id="결과창">
              {winBalls.map( (v) => <Ball key={v} number={v} />)}
          </div>
          <div>보너스!</div>
          {bonus && <Ball number={bonus} />}
          {redo && <button onClick={onClickRedo}>한 번 더!</button>}
      </>
  );
}
```
이렇게 되면
Lotto가 부모 component, Ball이 자식 component가 될것이다.   
   
#### (2) props에 함수를 넘길때
```js
const onClickRedo = () => { ... };

...

{bonus && <Ball number={bonus} onClick={onClickRedo}/>}
```
이런식으로 부모가 선언한 함수를 props로 자식에게 넘길 때를 말한다
   
#### (3) useCallback을 써야 한다.  
```js
const onClickRedo = useCallback( () => { ... }, []);

...

{bonus && <Ball number={bonus} onClick={onClickRedo}/>}
```
만약 useCallback을 안쓴다면, 부모 컴포넌트의 랜더링이 새로 일어날 때마다 함수도 새로 선언 된다. 이렇게 되면 자식 컴포넌트는 "어? 새로운 함수가 선언됐네? 다시 랜더링 해야겠다"라고 생각하고 계속 re-rendering 하게된다. 따라서 불필요하게 자식 컴포넌트가 re-rendering 되지 않도록, props로 넘길 함수에다가 useCallback을 써줘야 한다. 그래야지 "아 props로 넘어온 함수 그대로네. re-rendering 할 필요 없겠다" 라고 자식 컴포넌트가 적절히 처리할 수 있다.    


# 6-6 Hooks에 대한 자잘한 조언들

## 1. hooks에서 state 선언하는 useState는 조건문, 함수, 반복문 안에 넣으면 절대로 안된다. 
실행 될 수도 있고, 안될 수 있고, 실행 순서도 바뀔 수 있는 상황을 만들면 안된다.  
반복문이나 함수 안에다가 넣어도 되긴한다. hooks 순서가 확실히 정해진 경우에는 반복문이나 함수 안에 넣어도 되기는 하는데, 웬만하면 그냥 최상위에다가 useState 선언해주는게 좋다.  

## 2. useMemo, useCallback 잘 쓰자
useMemo와 useCallback으로 성능 최적화 잘 해야 고급으로 나아갈 수 있다.  
  
## 3. useEffect 잘 배우자
useEffect는 여러 번 쓸 수 있다. 이를 잘 활용해서 componentDidMount DidUpdate WillUnmount 를 useEffect로 잘 구현하면 된다.  


---

# 7 틱택토
# 7-1 틱택토와 useReducer 소개
state가 많아지면 줄여야 하지 않겠는가? 그게 바로 useReducer다.  
이게 좀 Redux의 방식과 같다. 그래서 오히려 Redux에 대한 설명을 보면 이해할 수 있다. [Redux 설명 영상](https://www.youtube.com/watch?v=QZcYz2NrDIs)    

# 7-5 테이블 최적화하기
useEffect랑 useRef로 뭐 때문에 re-rendering 되는지 파악해볼 수 있다. (강의 7-5 3:30)  


# Ch7에서 주요한 내용은 useReducer
redux와 비슷한 기능을 하는 useReducer가 ch7에서 핵심이다. useReducer가 필요한 이유, 어떻게 해결했는지, 어떻게 동작하는지 등에 대해 강의내용이 좀 부실해서 별도로 정리해야겠다.  
