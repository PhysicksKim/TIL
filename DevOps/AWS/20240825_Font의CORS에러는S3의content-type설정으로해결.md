# Font 파일에만 CORS 에러 발생  

EC2 서버에서 <code>allow origins</code> 설정으로 static 파일 저장소를 지정해줬는데도  
계속해서 "폰트 파일에만" CORS 에러가 발생 했다.  

# S3의 MIME Type (content-type 헤더)  

![image](https://github.com/user-attachments/assets/f791f42a-ad00-4ec0-9ff1-d8b46deba773)  
  
![image](https://github.com/user-attachments/assets/dd0755b8-f015-4d0e-9bc5-e83ca3ab9a7e)  

![image](https://github.com/user-attachments/assets/b0ee5570-5ed2-474f-8b06-1a383bccdced)  

<code>Content-Type</code> 이 <code>binary/octet-stream</code> 로 되어있다.  

CORS 정책 자체는 content-type 과 관련없어 보이는데  
아마도 예상되는 파일 타입과 다르기 때문에 브라우저가 차단시켜 버릴수도 있다 생각해서  
폰트 파일들의 content-type 을 수동으로 설정해줬다.  
  
<br><br> 

# font 파일들 Content-Type 설정  

![image](https://github.com/user-attachments/assets/603706d7-56a9-4924-aa2e-f22ce9e2d41f)  
  
![image](https://github.com/user-attachments/assets/edf97a78-4aea-47a2-8f84-f3e0ed3d7fce)  
  
Content-Type 을 font-otf 로 설정해주고 헤더가 제대로 들어옴을 확인했다.  
(cloudfront 에서 캐시 무효화 필요)  
