# 문제 발생   

```typescript
<video className='concert-video' muted autoPlay loop>
  <source src='https://static.gyechunsik.site/etc/gyechunhoe_concert_clips_for_webmain_720p_noaudio.mp4'></source>
</video>
```
위와 같이 <code>\<source\></code> 태그 내부에서 곧바로 src 로 url 을 통해 mp4 영상을 가져오고 있다.  
이렇게 해도 정상 동작에 문제가 없지만,  
문제는 영상 로딩에 시간이 걸린다는 점이다.  
  
사용자가 버튼 클릭 후 dom mount 후에 곧바로 영상을 보여주고 싶지만, 영상 용량이 수십mb 에 해당하기 때문에  
최초 방문 시점에 캐싱이 미리 이뤄져 있지 않아서 영상 로딩이 느려진다.  
  
재방문시 browser cache 에 의해서 영상이 딜레이 없이 보이겠지만  
최초 방문 시점에도 UX 개선을 위해서 "미리 영상을 로딩해둘 방법" 이 필요하다.  
  
<br><br>

# 실패한 해결법  
  
- 숨겨둔 video tag 에서 미리 로드하면 caching 이 이뤄지지 않을까?

```jsx
<>
  <div className='video-preload'>
    <video
      className='concert-video-preload'
      muted
      preload='auto'
      style={{ display: 'none', visibility: 'hidden' }}
    >
      <source src='https://static.gyechunsik.site/etc/gyechunhoe_concert_clips_for_webmain_720p_noaudio.mp4'></source>
    </video>
    <video
      className='football-video-preload'
      muted
      preload='auto'
      style={{ display: 'none', visibility: 'hidden' }}
    >
      <source src='https://static.gyechunsik.site/etc/gyechunsik_mainpage_football_noaudio.mp4'></source>
    </video>
  </div>
</>
```  
위 처럼 ".video-preload" 를 만들어서 영상들이 미리 로드되지 않을까 기대했다.  
하지만 caching 이 이뤄지지 않고 visibility 속성의 영향을 받는 것으로 보아서,  
이러한 방식을 이용해 캐싱하려면 video player 크기를 작게하여 사용자가 눈치채지 못하게 플레이 하는 등의 편법이 필요해 보였다.  

<br><br>  

# 개선된 해결법 - Blob url 

## Blob 과 Blob URL 로 해결할 수 있는 이유
     
Blob 은 file binary 를 다루듯 Object 를 load 하여 메모리상에 적재하기 위한 형식이다.  
Blob 을 이용한다면 Video Source 태그에서 Blob 을 사용하도록 하여 video load 문제를 개선할 수 있다.    

기존에 <code>\<source src="..." /></code> 와 같이  
HTML 태그에서 곧바로 URL 을 통해 resource load 시 캐싱 및 pre-load 에 어려움이 있었다.  
게다가 video - source 태그들은 곧바로 javascript object 에 담긴 video 객체를 넘겨주면 인식하지 못했다.  
무슨 말이냐면 <code> const videoLoad = fetch(...)</code> 로 비디오 파일을 가져오더라도  
<code>\<source src=videoLoad /></code> 로 fetch 해온 video 객체를 사용할 수 없었다. 
이는 HTML tag 의 src 속성은 "URL"만 인식할 수 있기 때문이다.  
    
<br>  
  
### Blob URL 로 해결  
Blob URL 은 "Blob 객체를 메모리에 저장해두고, 해당 메모리 주소를 URL 처럼 변환"하는 것을 말한다.  
즉, 일반적으로 object 변수는 메모리 참조 주소를 가지고 있는데  
HTML 은 src 속성에 Javscript 객체의 메모리 주소로 동작하지는 못한다.  
그래서 Blob 객체 참조를 URL 형태로 바꿔준 것을 Blob URL 이라고 한다.  
  
> Blob URL 은 이전에 WebWorker 에서 CORS 문제 회피를 위해서 사용한 경험도 있다.  
> [web worker CORS 우회를 위한 Blob URL 사용 사례](https://github.com/PhysicksKim/TIL/blob/main/JavaScript/20240515_WebWork%EC%82%AC%EC%9A%A9%EC%8B%9CCORS%EC%97%90%EB%9F%AC_blobURL%EB%A1%9C%ED%95%B4%EA%B2%B0.md)  

Blob URL 을 활용한다면 <code>\<source src="blob:...url..." /></code> 로  
javascript 에서 pre-load 한 blob object video source 를 사용할 수 있다.  
  
<br>

## Blob URL 을 통한 해결 코드  
   
```typescript
const response = await fetch('your-video-url', {
  cache: 'force-cache',
});

const videoBlob = await response.blob();
const videoUrl = URL.createObjectURL(videoBlob);
```

```tsx
<video width="600" controls>
  <source src={videoUrl} type="video/mp4" />
</video>
```
  
1) fetch api 에 의해서 browser caching 이 이뤄진다.
2) responses 를 blob() 으로 변환한다.
3) URL.createObjectURL(blob) 을 이용하여 "Blob URL" 로 변환한다.
4) <code>\<source></code> 태그에서 Blob URL 변환된 videoUrl 을 사용하도록 한다.
  
이를 통해서 아래와 같은 이점을 얻을 수 있다.  

1. browser caching 가능   
2. video 태그가 들어간 dom 이 mount 되기 전에, video resource 를 pre-load 하여 UX 를 개선시킬 수 있다.

<br>  

## 개선 - 사용자가 blob url 생성 전 video 태그를 로드시켜버리면  
  
개발자의 의도는    
  
1. 페이지 입장
2. video fetch 완료
3. blob url 생성 완료
4. 사용자가 video 태그 mount, 미리 생성된 blob url 바탕으로 비디오 출력
  
위와 같은 순서대로 동작하기를 의도했을 것이다.  
  
하지만 뜻하지 않게  

3. 사용자가 video 태그 mount, 미리 생성된 blob url 이 없음
4. blob url 생성 완료

이와 같은 순서대로 실행된다면 문제가 생길 것 같다.  
  
그래서 react 를 활용하여, video tag 에 연결된 useRef 를 만들고  
blob url state variable 이 update 될 때마다   
video tag 에게 reload 명령을 내리도록 한다.  
  
```tsx
<div>
  <video ref={videoRef} width="600" controls>
    <source src={videoSrc} type="video/mp4" />
  </video>
</div>
```
   
이렇게 하면, 일시적으로 빈 video 가 보일 순 있겠지만   
blob url 생성 이후에는 곧바로 video 태그가 업데이트 되어서 자동으로 blob url 생성 후에 비디오가 재생되도록 할 수 있다.   

> 필요하다면 blob url 생성 이후에 flag boolean 을 true 로 변경시켜서
> flag 에 따라서 video mount 시키는 버튼이 동작하지 않도록 해버릴 수도 있다.  

<br>  

# 정리  
- video 태그 에서 사용할 영상을 미리 로드 하려면 Blob URL 을 활용하면 된다.  
- Blob URL 은 fetch 한 객체를 blob 형식으로 변환한 다음, 이를 다시 메모리 주소 참조가 아닌 "URL" 형식 주소로 참조할 수 있도록 하는 방법이다
- video 태그에서 자원을 명시하는 source 태그의 "src" 속성에서는, URL 형식만 사용할 수 있다.
- blob url 을 사용하는 이유는 "메모리 주소 참조" 형식을 "URL" 형식으로 바꿔서 오브젝트를 가져오도록 하기 위해 사용되었다.
- 그래서 blob url 을 source 태그의 src 속성에 넣어주면, javascript 로 미리 불러온 video 자원을 사용할 수 있게 된다.  
   
<br><br>  

---
    
<br><br>  

## 코드 전문

### 부모 컴포넌트 - video fetch 를 담당  
```tsx
import React, { useState, useEffect } from 'react';
import VideoComponent from './VideoComponent';

function ParentComponent() {
  const [videoSrc, setVideoSrc] = useState(null);

  useEffect(() => {
    const loadVideo = async () => {
      try {
        const response = await fetch('https://static.gyechunsik.site/etc/gyechunhoe_concert_clips_for_webmain_720p_noaudio.mp4', {
          cache: 'force-cache',
        });
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }

        const videoBlob = await response.blob();
        const videoUrl = URL.createObjectURL(videoBlob);
        setVideoSrc(videoUrl);
      } catch (error) {
        console.error('Failed to load video:', error);
      }
    };

    loadVideo();
  }, []);

  return (
    <div>
      {!videoSrc ? (
        <div>Loading...</div>
      ) : (
        <VideoComponent videoSrc={videoSrc} />
      )}
    </div>
  );
}

export default ParentComponent;
```

### 자식 컴포넌트 - 화면 렌더링 담당  
```tsx
import React, { useEffect, useRef } from 'react';

const VideoComponent = ({ videoSrc }) => {
  const videoRef = useRef(null);

  useEffect(() => {
    if (videoSrc && videoRef.current) {
      // videoSrc가 변경될 때마다 비디오를 다시 로드
      videoRef.current.load();
    }
  }, [videoSrc]);

  return (
    <div>
      <video ref={videoRef} width="600" controls>
        <source src={videoSrc} type="video/mp4" />
        Your browser does not support the video tag.
      </video>
    </div>
  );
};

export default VideoComponent;
```
