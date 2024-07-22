# Alias 추가 방법  

1. Webpack 에서 resolve.alias 추가
2. tsconfig.json 에서 paths 에 alias 경로들 추가
  
<br><br>  

# 1. Webpack Config 에 Alias 추가 

```javascript
  resolve: {
    alias: {
      '@app': path.join(webpackPaths.srcRendererPath, 'app'),
      '@matchlive': path.join(webpackPaths.srcRendererPath, 'matchlive'),
    },
    extensions: [
      '.js',
      '.ts',
      '.jsx',
      '.tsx',
      '.json',
      '.css',
      '.scss',
      '.sass',
    ],
  },
```

alias 와 extensions 를 추가해줬다. 
extensions 는 alias 를 사용한 경로에서 인식할 파일들의 확장자를 지정해준다.  

webpack config 는 대체로 dev 와 prod 로 나뉘어 있을텐데  
dev, prod 둘 다 alias 설정을 적어줘야한다.  
  
<br><br>  

# 2. tsconfig.json  
  
타입 스크립트 컴파일 과정에서 alias 를 인식해야 하므로 tsconfig 에서도 alias 를 동일하게 적어줘야한다.  
  
```json
"paths": {
  "@app/*": ["./src/renderer/pages/app/*"],
  "@matchlive/*": ["./src/renderer/pages/matchlive/*"]
}
```

tsconfig.json 파일이 위치한 경로를 기준으로 각 alias 가 지칭하는 위치를 위와 같이 paths 에 적어준다. 
  
