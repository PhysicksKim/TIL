# image 파일 import 해서 쓰기

```
import myImage from './img/image01.png'

...
return(
  <img src={myImage} />
);
```
styled-component에서 img 사용법에 대해 검색하다보니 저런식으로 이미지를 import해서 쓰는 예시를 많이 봤다. 그런데 내가 하니 unexpected character라고 이미지 파일들을 import할 때 아예 인식 자체를 못하는 에러를 보여주었다. 그래서 검색해보니 이런 방식으로 이미지 파일을 import해서 쓰기 위해서는 file-loader를 사용해야한다고 한다. file-loader를 dev dependencies에 추가하고, webpack에서 module: rules: 에다가 file-loader에서 어떤 파일을 인식할지 설정해주면 된다.  
  
#### 1. file-loader 패키지 추가
```
yarn add -D file-loader
```  
성공적으로 완료되면 package.json에서  
```
"devDependencies": {
    ...
    "file-loader": "^6.2.0",
    ...
}
```
라고 된 것을 볼 수 있다.  
  
#### 2. webpack.config.js에 file-loader 설정 추가
```
module.exports = {
  ...
  module: {
    rules: [{
      ...
      },{
          test: /\.(gif|svg|jpg|png)$/,
          loader: "file-loader",
      }],
  },
  ...
};
```
gif svg jpg png 파일을 인식할 수 있도록 정규표현식과 loader를 추가해준다.  


---
