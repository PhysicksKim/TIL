### 문제 : Electron 커스텀 프로토콜 사용 시 외부 http/https 로 요청이 안 되는 문제    
### 해결 : 외부 http/https 서버측에 커스텀 프로토콜에 CORS 설정 필요  

---

## 요약 : custom protocol 설정시 CORS 문제 발생할 수 있음  

file protocol 에서 온 요청은 cors 가 일어나지 않았으나  
custom protocol 에서 온 요청은 allow origin 에다가 custom protocol 을 allow origin 으로 세팅해줘야한다.   
  
---

<br><br>

# 커스텀 프로토콜 설정 이유  

file protocol 은 xss 공격이 침투했을 때 로컬 파일로 접근할 수 취약점을 만든다.    
따라서 custom protocol 을 정의하고 여기서 파일 경로 접근 권한을 프로그램 폴더 이하로 제한하도록 해야한다.  
[electron protocol](https://www.electronjs.org/docs/latest/api/protocol)  
[electron docs best practice #18 avoid file protocol](https://www.electronjs.org/docs/latest/tutorial/security#18-avoid-usage-of-the-file-protocol-and-prefer-usage-of-custom-protocols)  
  
그러므로 내 앱 이름을 따서 `chuncity://` 프로토콜을 정의했는데 문제는 api 서버로 https 요청을 보낼 때 axios 에서 `403 network error` 를 반환한다는 점이다.  
  
이에 따라 여러 가지를 추측해보고 커스텀 프로토콜의 권한문제인지 아니면 https 요청이 막히는지 등을 찾아봤는데  
결론은 서버측 CORS 설정에서 커스텀 프로토콜 문제였다.  

<br>  

# custom protocol 설정 과정 설명 

아래와 같이 custom protocol 을 설정했다.  
아래 과정을 통해서 package 이후에 앱 ui (즉 html css js) 가 제대로 동작하는 것 까지는 확인했으나  
axios 로 api 서버에 https 요청은 403 에러가 발생했다.  
  
우선 설정 과정부터 보자.  
  
## custom protocol 정의

```typescript
export const CUSTOM_PROTOCOL_NAME = 'chuncity';

protocol.registerSchemesAsPrivileged([
  {
    scheme: CUSTOM_PROTOCOL_NAME,
    privileges: {
      standard: true,
      secure: true,
      supportFetchAPI: true,
      corsEnabled: true,
      stream: true,
    },
  },
]);
```

```typescript
app
  .whenReady()
  .then(async () => {
    protocol.handle(CUSTOM_PROTOCOL_NAME, async (request) => {
      try {
        const parsedUrl = new URL(request.url);
        let relativePath = decodeURIComponent(parsedUrl.pathname);

      // ...상세 코드 생략...

        const fileURL = pathToFileURL(normalizedPath).toString();
        const response = await net.fetch(fileURL);
        return response;
```

위와 같이 커스텀 프로토콜을 정의하고 net.fetch 로 file 을 가져와서 response 하도록 했다.  
  
<br>  

## Browser Window 에서 html 로드 시 custom protocol 사용하도록 설정    


electron `BrowserWindow` 에서 `loadUrl(...)` 에서 아래와 같은 형태로 custom protocol 을 사용해 `index.html` 파일을 로드하도록 했다.   
  
```typescript
mainWindow.loadURL(resolveHtmlPath('index.html'));
```

```typescript
import { URL } from 'url';
import { CUSTOM_PROTOCOL_NAME } from './main';

export function resolveHtmlPath(htmlFileName: string) {
  if (process.env.NODE_ENV === 'development') {
    const port = process.env.PORT || 1212;
    const url = new URL(`https://localhost:${port}`);
    url.pathname = htmlFileName;
    return url.href;
  }
  return `${CUSTOM_PROTOCOL_NAME}://chuncity.app/${htmlFileName}`;
}
```

위 코드에서 핵심은 `resolveHtmlPath('index.html')` 의 결과로 `chuncity://chuncity.app/index.html` 을 loadURL 로 설정하게 된다는 점이다.  
즉 커스텀 protocol 이 동작하도록 하기 위해서 `chuncity://chuncity.app/` 이하로 파일들이 위치하도록 하며,  
custom protocol handler 에서 url 의 path 를 추출해서 경로를 검사한 후 파일들(html js css 등)을 제공해주도록 했다.  
  
> 여기서 protocol 만 두지 않고 chuncity.app 을 끼워넣어 둔 이유는 (protocol://host/path...)  
> protocol 만 두면 url 파싱 과정에서 protocol + host + path 로 파싱되어야 하는 과정에서 host 가 없어서 path 에 들어가야 할 내용이 host 로 들어가서 파싱되어 버리기 때문이다.   
  
<br>  

### 결과 : package 이후 정상적으로 custom protocol 을 거침이 확인됐으나 외부 https api 응답에는 문제 발생   

renderer(=browser window) 에서 axios 요청으로 https 요청을 보냈는데 `403 forbidden` 이 발생했다.  
  
<br><br>  

# 원인 : API 서버 측 CORS 문제  

기본적으로 내 API 서버에서는 CORS allow origin 에 대해서 와일드카드(*) 로 선언하지 않고 수동으로 지정해주고 있다.  

그래서  

### 커스텀 protocol 을 포함해 커스텀 프로토콜에서 사용한 임의 host 이름 까지 CORS allow 설정 해줘야 한다.  
  
따라서 아래와 같이 cors allow origin 으로 설정해주고 문제를 해결했다. 

```yaml
# 운영 환경에서 허용할 CORS 출처
cors:
  allowedorigins: "https://physickskim.github.io,https://gyechunsik.site,https://static.gyechunsik.site,chuncity://chuncity.app"
```

```java
@Slf4j
@Configuration
public class WebConfig {

    @Value("${cors.allowedorigins}")
    private String rawAllowedOrigins;

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        String[] origins = rawAllowedOrigins.split(",");
        log.info("Web Config allowed origins : {}", Arrays.toString(origins));

        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**")
                        .allowedOriginPatterns(origins)
                        .allowedMethods("GET", "POST", "PUT", "DELETE", "PATCH", "OPTIONS")
                        .allowedHeaders("*")
                        .allowCredentials(true);
            }
        };
    }
}
```

위 코드로 `chuncity://chuncity.app` 를 `alloworigins` 환경변수에서 설정해주고       
이를 `WebConfig` 에서 `allowedOriginPatterns(origins)` 로 넣어줘서 cors allow origin 을 세팅해줬다.   
  
위는 spring boot 3 기준 cors allow origin 세팅 예시이며  
각 백엔드 프레임워크 및 상세 버전에 따라서는 cors allow origin 세팅이 다를 수 있다.  
특히 위 spring 예시 에서는 `allowedOriginPatterns()` 을 사용했는데  
custom protocol 에서 allow origin 세팅이 제대로 동작하는지에 대한 문제도 있을 수 있음을 고려해야 한다.  
