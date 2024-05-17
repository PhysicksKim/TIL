# CORS 에러 발생 과정과 이유  
   
ScoreBoard 에서 WebWorker 사용 후, 로컬 테스트에서는 CORS 문제가 생기지 않았지만   
빌드 배포 후 실 서비스에서 문제가 발생했다.   
  
원인을 알아보니  
webpack 에 의해서 번들링 된 이후에  
Main.\[hash].js 파일에서 WebWorker.js 파일을 불러오도록 되어있는데  
이렇게 되면 js 파일이 웹 주소로 다른 js 파일을 가져와서 실행하는 꼴이 되므로 위험하다고 판단하여 CORS 에러를 발생시킨다.  
이는 CDN 이나 Web App Server 측에 각각 둘 다 CORS allow origins 에 url 을 추가해줘도 마찬가지로 문제가 발생한다.  
    
### 이유  
브라우저에서는 Js 파일에서 다른 js 파일을 불러와 실행시키는 것을 위험한 동작으로 간주하여 원천적으로 차단한다. 

<br><br>  

# 해결 
### js 파일 불러와서 blob 형태로 변환하고, blob 형식 객체를 다시 js 로 변환하여 webworker 에 넣어줌  

```javascript
// 외부 스크립트 URL
const cross_origin_script_url = 'https://static.mydomain.com/worker.js';

// Blob URL을 생성하는 함수
function getWorkerURL(url) {
  const content = `importScripts("${url}");`;
  return URL.createObjectURL(new Blob([content], { type: 'application/javascript' }));
}

// Blob URL을 사용하여 Web Worker 생성
const worker_url = getWorkerURL(cross_origin_script_url);
const worker = new Worker(worker_url);
worker.onmessage = (event) => {
  console.log(event.data);
};

// 사용 후 Blob URL 해제
URL.revokeObjectURL(worker_url);
```

## 코드 설명    
핵심은 <code>URL.createObjectURL(new Blob(...))</code> 를 통해서,    
"""불러온 js 내용을 마치 코드 내부에서 생성한 js 코드 라인인 것 처럼""" 처리하는 것이다.   
    
그러면 여기서 <code>new Blob(...)</code> 생성자 안에 들어갈 Blob 의 content 를 어떻게 가져올 것인가?    
    
여기에는 위 코드에서는 importScript() 함수를 사용했고,    
또 다르게는 단순히 fetch() 하여 파일을 가져온 후 text(=payload)를 그대로 Blob Content 로 넣어서 생성할 수 있다.    
   
> ### importScript()  
> importScript() 함수는, 해당 url 에서 스크립트를 가져온 다음,  
> 스크립트를 곧장 실행하는 함수에 해당한다.  
> 따라서 단순하게 해당 라인이 url js 파일 내용으로 대체되는 것이라 생각하면 된다.  
  
### fetch() 사용  

단순하게 fetch() 를 사용해서 파일을 가져온 다음, 파일의 payload 를 추출해서  
마치 webworker 에 텍스트를 그대로 넣을 수도 있다.  
  
```typescript  
export class CorsWorker {
  private readonly url: string | URL;
  private readonly options?: WorkerOptions;

  constructor(url: string | URL, options?: WorkerOptions) {
    this.url = url;
    this.options = options;
  }

  async createWorker(): Promise<Worker> {
    const f = await fetch(this.url);
    const t = await f.text();
    const b = new Blob([t], {
      type: 'application/javascript',
    });
    const url = URL.createObjectURL(b);
    const worker = new Worker(url, this.options);
    return worker;
  }
}
```
  
단지 importScript() 와 차이는 importScript 는 WebWorker.js 파일을 가져와서 해당 라인이 가져온 WebWorker 내용으로 대체된다는 것이고  
fetch() 는 그대로 js 파일의 text 로 추출하여서 webWorker 안에 넣어버리는 것이다.  
  
두 방식 다 별 차이 없이, 그냥 Blob Constructor 안에 content 로 js script 내용을 넣을 수 있기만 하면 된다.  

>  심지어 js script webworker 를 그냥 raw string 으로 하드코딩해서 넣어도 동작한다.  
