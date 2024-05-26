![image](https://github.com/PhysicksKim/TIL/assets/101965836/5f1c8d94-a200-4991-bb53-497efb32e4e7)  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/4026f39a-e485-44e6-b171-e46c6d165967)  
  
위와 같이 403 forbidden 이 발생하고 직접 브라우저에서 주소로 접근하면 AccessDenied 가 발생한다.  
  
<br><br> 

---

<br><br>  
  
# 설정 상태  
  
## 0. Cloudfront - S3 연결해야 함  
S3 는 기본적으로 퍼블릭 접근을 열어두는 자체가 좋지 않을 뿐더러  
S3에 저장된 파일에 보안이 필요없다 하더라도  
S3 는 저장에 특화되어있고 "많은 읽기" 에는 요금 책정이 CloudFront 보다 크게 된다.  
따라서 "읽기" 에 특화된 CloudFront 로 캐시를 구성해두는 게 무조건 좋다.   
어차피 어렵지도 않고, 더불어 무료티어를 챙기려면 S3를 퍼블릭으로 두면 금방 읽기 횟수를 초과하니까  
꼭 S3 앞에 CloudFront 를 두자.    
  
## 1. S3 퍼블릭 접근 차단  

![image](https://github.com/PhysicksKim/TIL/assets/101965836/3382b593-5bee-4d39-ae23-536909264bbf)  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/dba88b94-857f-42f0-a75f-6dcc37bf72ca)  
  
S3 권한 탭에서 "퍼블릭 액세스 차단" 에서 모든 퍼블릭 액세스를 차단해주자.  

<br>  

## 2. CloudFront 측 설정  

### S3 원본 액세스 설정  
  
CloudFront 배포 - 설정하고자 하는 CloudFront 배포 선택 - "원본" 탭 - 해당 S3 원본 선택 후 "편집"  
   
![캡처2](https://github.com/PhysicksKim/TIL/assets/101965836/b8ac470f-400b-4fba-9575-1016cdf637ef)    
  
### S3 측, 정책 설정  
  
앞선 원본 액세스 설정 아래 파란 박스에 있는 "정책 복사" 를 클릭하면 S3에 사용할 정책이 복사된다  
이후 파란 박스 바로 아래에 "S3 버킷 권한으로 이동" 을 클릭하면 S3 버킷으로 이동한다  
"권한" 탭에서 "버킷 정책" 에 편집을 누르고  
앞서 복사한 정책을 붙여넣기 하면 된다.  

![캡처3](https://github.com/PhysicksKim/TIL/assets/101965836/e553cc01-cd1e-4faf-9508-2d0f4053f6e9)

  
<br><br>  
  
# 해결 과정

# 1. CORS 정책 설정

[레퍼런스](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ManageCorsUsing.html#cors-example-2)  
  
S3 버킷 - 권한 - "CORS(Cross-origin 리소스 공유)" 에서 "편집" 을 눌러 설정  
   
```
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "GET",
            "HEAD"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": [
            "Access-Control-Allow-Origin",
            "x-amz-server-side-encryption",
            "x-amz-request-id",
            "x-amz-id-2"
        ],
        "MaxAgeSeconds": 3000
    }
]
```

- ExposeHeaders 의 x-amz 헤더들  
  x-amz 헤더들은 각각 서버측 암호화 유형, 요청 아이디, 추가적인 요청 식별자(id-2) 에 대항하는 헤더이다.   
- MaxAgeSeconds 는 preFlight 요청 결과를 얼마동안 캐싱할 지를 나타낸다   
  
<br>  
  
# 2. Cloudfront Behavior 설정  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/4baabac4-87fc-4ae4-b200-92e627433562)  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/44547c73-548e-4b1a-9ba9-e897922d65e7)  
   
연결된 Cloudfront 의 "동작" 탭에서 모든 경로(\*)에 대하여 위와 같이 설정했다.   
   
HTTP 와 HTTPS 로 들어오는 모든 요청에 대해서    
"캐시 키 및 원본 요청" 에서 "CORS-S3Origin" 로 설정해줬다.  
  
> ### CORS-S3Origin 설정의 의미  
> S3 Origin 에 대해서 CORS 를 허용해주는 설정이다  
> 아래의 헤더들을 포함해준다  
> [레퍼런스](https://docs.aws.amazon.com/ko_kr/AmazonCloudFront/latest/DeveloperGuide/using-managed-origin-request-policies.html#managed-origin-request-policy-cors-s3)  
> - origin  
> - access-control-request-headers  
> - access-control-request-method  
  
<br>  
  
