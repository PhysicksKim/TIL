# 선 요약 
Video 태그는 기본적으로 조각씩 다운 받아 재생한다  
  
<br><br>  

---

<br><br>  
  
# video 태그  

```html
<video controls width="250">
  <source src="/myvideo.mp4" type="video/mp4" />
</vide>
```
  
video 태그에서 mp4 파일을 사용했다.  
근데 기본적으로 mp4 파일은 통쨰로 재생된다. 스트리밍 타입과는 다르다.  
그런데 별다른 처리를 하지 않아도, 브라우저와 웹서버들이 알아서 **Progressive Download** 를 지원해준다.  
다운받으면서 영상을 재생할 수 있다는 것이다. 이게 기본 동작으로 들어가 있다.  
  
# Progressive Download  
비디오 파일을 전부 다운받아야 재생할 수 있다면, 재생까지 한참 기다려야 할 것이다.  
그래서 Progressive Download 라고 해서, 조각조각 다운 받으면서 곧장 재생할 수 있도록 구현되어 있다.  
웹 개발자는 별다른 처리를 하지 않아도 Web Browser, CDN, Webserver 등에서 기본적으로 MP4 와 같은 대표적인 파일 형식과 코덱에 대해서 Progressive Download 방식 비디오 재생을 지원해준다.  
  
<br><br>  

# BlobUrl vs 그냥 Video 태그  
처음에는 "미리 다운 다 시켜버리고 BlobUrl 로 메모리에 갖고 있으면 좋지 않을까?" 라고 생각했다.  
이론상 첫 페이지에 들어오자 마자 UX 를 고려하여 곧바로 비디오가 뜨도록 할 수 있으니  
미리 다운을 시켜버리고 메모리에 저장해두는 BlobUrl 방식이 굉장히 유용하리라 생각했다.  
  
하지만 한계가 있었다.  
내 프로젝트에서 사용했던 MP4파일은 약 30MB 였는데,   
30MB 비디오를 다운받고 blobUrl 로 변환하기까지 시간이 제법 걸렸다.  
그래서 그 사이에 사용자가 Video 태그에 접근하면, 사용자는 빈 화면은 몇 초가 쳐다봐야 한다는 문제가 있었다.  
   
반면 기본 video 태그는 progressive download 를 지원하기 떄문에     
영상의 첫 부분이 거의 http 응답 속도 수준으로 곧바로 출력된다.     
progressive 조각이 어떻게 나뉘는 지는 확인하지 못했는데    
사용자는 깜빡이는 수준으로 빈 화면이 보게 됐다.  
     
그래서 그냥 video 태그를 사용하기로 했지만   
버튼 클릭 후 DOM mount 되는 순간 빈 화면이 깜빡이는 수준으로 보이는 것도 신경 쓰였는데  
이를 "숨겨둔 video 태그" 로 해결했다.  
  
<br><br>  
  
# video 태그 숨겨두기  

프로젝트에서 video DOM 마운트 후 비디오 최초 재생은 아래와 같이 이뤄진다.  
  
1. 버튼 클릭   
2. React Component 마운트   
3. video 태그에 따라 source mp4 파일 다운   
4. cdn 에서 최초 mp4 progressive download 조각 전달   
5. 사용자 화면에서 재생 시작    
  
여기서 문제는  
cdn 에서 최초 비디오 조각을 전달받기 전까지  
사용자는 빈 화면을 봐야 한다는 점이다.  
  
<code>\<video preload ... </code>로 preload 를 이용하면 되지 않느냐 하지만  
이는 dom 이 마운트 되기 전에 video 가 preload 가 이뤄지지 않기 때문에 크게 의미 없다.  
내가 원하는 동작은 <code>버튼 클릭-dom mount-곧바로 비디오재생</code> 이기 때문이다.  
  
파일 자체를 메인 페이지 입장 하자마자 fetch 해버리면 브라우저가 캐싱해주지 않을까? 생각 했는데  
생각보다 간단하게 그냥 video 태그를 숨겨두는 식으로 해결해버릴 수 있었다.  
  
<br>  

## video 숨겨서 preload  

```html
<video
  preload='auto'
  autoPlay
  muted
  style={{ display: 'none' }}
>
  <source src=...></source>
</video>
```
그냥 display none 으로 해두고 preload 하도록 하면 됐다.  
이렇게 하고 개발자 도구의 네트워크 탭을 확인하니  
preload 로 mp4 파일을 미리 다운 받는 것을 확인할 수 있었다.  
  

