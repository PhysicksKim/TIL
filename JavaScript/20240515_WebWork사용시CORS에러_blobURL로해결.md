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
