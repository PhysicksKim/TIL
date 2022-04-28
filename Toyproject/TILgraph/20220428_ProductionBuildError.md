# production 모드로 빌드 안되던 문제 해결

<h1>1줄 요약 : 배포용 빌드에는 react-refresh/babel 플러그인 넣으면 안됩니다</h1>  

webpack으로 빌드시에 그냥 mode: 'production' 으로만 바꾸면 된다고 했는데,  
자꾸 빌드만 하면 빈 화면만 나와서 무슨 문제인가 궁금했다.  
  
  
일단 결론부터 말하면  
```
module: {
    rules: [
      {
        test: /\.jsx?./,
        loader: 'babel-loader',
        options: {
          presets: ['@babel/preset-env', '@babel/preset-react'],
          plugins: ['react-refresh/babel', '@babel/plugin-transform-runtime'],
        },
      },

      ...

```
여기서 **'react-refresh/babel'** 가 문제였다.  
이걸 제거하니 해결됐다  
  
  
아마 저 react-refresh/babel이 webpack production용 빌드 과정에서 문제를 일으키는 모양이다.   
에러가 있을 때 콘솔 창을 보니까   
```
$RefreshReg$ is not defined 
```
이런 에러가 났어서 검색해보니  
[여기](https://github.com/pmmmwh/react-refresh-webpack-plugin/issues/176) 이런 내용이 있었다  
  
솔직히 저 글이 뭔소린지 잘 모르겠지만   
아무튼 react-refresh/babel 이 언급된 것을 보고  
"아 맞다 저거 핫 리로딩 관련된거니까 production에 필요 없는데... 혹시 저게 webpack production용 최적화 과정에서 문제를 일으킨건가?"  
라고 번뜩이는 뇌피셜을 떠올렸고  
  
  
딱 저 **'react-refresh/babel'** 없애주고, 
```
  ...
  mode: 'production', // 실서비스 : production
  devtool: 'hidden-source-map',
  ...
```
이렇게 해주니까  
production 빌드 대성공 굳  
