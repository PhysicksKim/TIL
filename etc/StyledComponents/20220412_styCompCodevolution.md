[강의로 이동](https://www.youtube.com/playlist?list=PLC3y8-rFHvwgu-G08-7ovbN9EyhF_cltM)  

# 2. 설치 및 세팅

#### (1) vscode기준 Extension으로 vscode-styled-components 설치
#### (2) yarn add styled-components

---

# 3. Styled Component 기초

## 기본 사용법
```js
import styled from 'styled-components';

...

const StyledButton = styled.button`
  color: blue;
  border 2px solid #4caf50;
  ...
`;
  
fuction App() {
  return(
    <>
      <StyledButton>Styled Button</StyledButton>
    </>
  );
}

export default App;
```

위와 같이 styled-components를 import하면, styled에서 html 태그들을 지정해 스타일을 \` \` 안에 템플릿 리터럴로 적어넣을 수 있다. 템플릿 리터럴은 곧 CSS안에 변수, 함수 까지 쓸 수 있게 된다는 의미이다. 생성한 Styled 태그는 react component와 같은 방식으로 사용할 수 있다. 위 예제 코드의 경우 <StyledButton />은 HTML의 button 태그 속성을 가지면서, 생성시 적어둔 템플릿 리터럴 안의 CSS 내용도 포함된 컴포넌트가 된다. 
  
## 파일로 분리
react에서 다른 파일에 component를 만들고 불러오는 것과 마찬가지로, styled-components로 다른 파일에서 태그를 생성하고 불러와서 사용할 수 있다. 
```js
import styled from 'styled-components';

const StyledHeadText = styled.h1`
    height: 100%;
    width: 100%;
    color: #aaaaaa;
`;

export default StyledHeadText;
```
이 같이 기존 react component와 동일한 방식으로 styled components도 export 할 수 있다.  

---

# 4. Props 사용법
styled-components는 props 정보를 자동으로 템플릿 리터럴 안으로 가져올 수 있다. 따라서 아래와 같은 코드도 가능해진다
```JS
<StyledButton variant='white'>Styled Butto</StyledButton>
```

```js
const StyledButton = styled.button`
  background-color: ${(props) => props.variant === 'white' ? '#FFF' : '#4caf50' };
`
```
이런식으로 인자로 props를 쓰고, 태그에다가 써넣은 변수를 그대로 쓸 수 있다.  

---

# 5. 생성한 styled를 재사용해서 확장

## 확장(Extending)이 뭐임?  
  
앞선 코드처럼 StyledButton을 만들었다고 해보자. 그러면 이걸 아래와 같은 방법으로 css를 추가해서 다시 사용할 수 있다.
```
const FancyButton = styled(StyledButton)`
  background-image: linear-gradient(to right, #f6d35 0%, #fda085 100%);
  border: none;
`
```

이런식으로 기존 속성을 그대로 받아오면서도, 확장(Extending)해서 새로운 styled component를 만들 수 있다.  

## 심지어 다른 태그로 확장 할 수 있다
놀랍게도 근본이 button 태그인데 이걸 a 태그로 바꿀수도 있다.  
```
<FancyButton as='a'>Fancy Button</Fancy Button>
```
이런식으로 as='a' 같은 식으로 다른 코드로 변환할 수 있다. 버튼 태그가 a태그로 도대체 어떻게 변할 수 있는지, 또 왜 이렇게 바꾸는 기능이 있는지 당최 알 수 없는데... 아무튼 바꿀 수 있다고 한다. 이걸 어디다가 써먹는지는 아직 감이 안온다.  

---

# 6. 의사 클래스(pseudo-class)
>## 잡담
>의사(疑似: 비교할 의, 비슷할 사) 클래스.  
>의사라는 말이 솔직히 와닿지는 않는다. 의사라는 단어가 천천히 생각해보면 무슨 뜻인지 느낌이 오긴 하는데, 차라리 pseudo가 더 직관적으로 이해되는 것 같다.
 
pseudo class란 예를 들면 StyledButton:hover 같은 것이다. CSS에서 미리 어떤 행동이나 상태가 일어나면 가상으로 클래스가 추가되고, 추가된 클래스에 따라서 어떤 CSS를 또 더 적용시키는 등으로 활용한다.  

Styled-Components에는 이러한 pseudo class를 더 편리하게 적용할 수 있다.   
기존에는 아래와 같이
```CSS
.OriginalButton{
  color: white;
}
.OriginalButton:hover{
  color: blue;
}
```
따로 또 적어줘야했기 때문에 번거롭고 코드도 지저분해졌다.  
  
하지만 Styled-Components에서는 
```js
const StyledButton = style.button{
  color: white;
  &:hover{
    color: blue;
  }
}
```
이렇게 &만 적고 pseudo class를 쓰면 된다.  
물론 마찬가지로 함수와 변수, props를 쓸 수 있으니, 매우 편리해진다.  

---

# 7. Passed Props and Adding Attributes

StyledButton을 위처럼 만들어 놨다고 하자. 그런데 여기다가 props중에서 type='submit'을 넣고싶다고 하자. 그러면 직관적으로 아래와 같이 하면 된다는 것을 알 수 있다.  
```
  <StyledButton type='submit'>Styled Button</StyledButton>
```
실제로 이렇게 하면 동작한다.  

## 그러나 attributes도 여럿 추가해서 styled-component를 만들 수 있다

```js
const SubmitButton = styled(StyledButton).attrs({
  type: 'submit',
})`
  background-color: aqua;
`
```
이렇게 .attrs를 사용하면 된다.  

## attrs를 사용하면 props로 변수 받아오는건 어떻게 할까?

```js
const SubmitButton = styled(StyledButton).attrs((props) => {
  type: 'submit',
})`
  background-color: aqua;
`
```

---

# 8. Animations

## 기존 CSS의 Animation Name 사용시 문제점
예를 들어 아래와 같이 logoSpin 라는 Animation을 .App-logo에 적용했다고 해보자.
```CSS
.App-logo{
  animation: logoSpin infinite 20s linear;
}
@keyframes logoSpin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}
```
그런데 기존의 CSS 방식은 수 많은 CSS 속성들과 클래스 이름, 애니메이션 이름이 겹치고, 또 한 파일로 몰리게 된다. 이에 따라 서로 다른 파일에서 애니메이션이 다시 정의되는 문제가 발생할 수 있다.  
  
```CSS
.App-logo{
  animation: logoSpin infinite 2s linear;
}
@keyframes logoSpin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}
```
이를 막기 위해서 Styled Components에서 제공하는 keyframes를 사용할 수 있다.

## StyledComponents의 keyframes 사용
```js
import styled, { keyframes } from 'styled-components';

...

const rotate = keyframes`
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
`

const AnimatedLogo = styled.img`
  animation: ${rotate} infinite 20s linear;
`
```
기존에는 @keyframe (식별자){ ... } 로 선언하고 animation-name으로 연결지어 줬다.  
그러나 keyframes를 사용하면 위와 같이 rotate라는 이름으로 할당하고 ${}로 넣어줄 수 있다.  
코드 관리 측면에서 훨신 편리해졌음을 알 수 있다.  

---

# 9. Theming

예를 들어보자. 웹 사이트에 다크버전을 추가해야하는 경우가 있을 것이다. 그러면 어두운버전, 밝은버전 두 가지로 CSS를 작성해야 할 것이다. 근데 또 그럴려면 props에다가 어떤 테마인지 정보를 담고, props에 담아서 하위컴포넌트로 보내고 또 props에 담아서 하위... 이렇게 계속 물려줘야 한다.  
이에 대한 해결책으로 Styled-components에서 Theming 이라는 contextAPI 기능을 제공한다!!   
사용법은 아래와 같다.  
[출처 공식문서](https://styled-components.com/docs/advanced)  
```js
// Define our button, but with the use of props.theme this time
const Button = styled.button`
  color: ${props => props.theme.fg};
  border: 2px solid ${props => props.theme.fg};
  background: ${props => props.theme.bg};

  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border-radius: 3px;
`;

// Define our `fg` and `bg` on the theme
const theme = {
  fg: "palevioletred",
  bg: "white"
};

// This theme swaps `fg` and `bg`
const invertTheme = ({ fg, bg }) => ({
  fg: bg,
  bg: fg
});

render(
  <ThemeProvider theme={theme}>
    <div>
      <Button>Default Theme</Button>

      <ThemeProvider theme={invertTheme}>
        <Button>Inverted Theme</Button>
      </ThemeProvider>
    </div>
  </ThemeProvider>
);
```
![image](https://user-images.githubusercontent.com/101965836/163207424-f4df458d-a82d-4850-89c4-c3d5f947621d.png)  
  
위 코드처럼 ThemeProvider를 사용해서 테마를 정해놓고 그에 맞춰 css를 적용시킬 수 있다.  
