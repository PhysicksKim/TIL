# 개요 - AWS 에서 S3 + CloudFront 로 CDN 구축 + Subdomain 과 연결

- S3 : 정적 파일 저장소. 원본 파일이 저장되어 있는 곳.  
- CloudFront : 정적 파일 저장소 앞에 두고 캐싱해두는 역할. 지역별 캐싱 서버 기능을 제공.  
(ex. 유럽, 미국, 한국 등 각 region에 가까이 위치한 곳)  

CloudFront 생성 후 원본소스를 S3 로 설정하고, 
SubDomain을 위해 ACM 에서 인증서 발급 후 Route53 설정과 더불어 가비아(도메인 발급 업체)에서 설정도 필요하다.  

> ### 참고
> 
> [CloudFront 와 SubDomain 연결](https://three-beans.tistory.com/entry/AWSCloudFront-CloudFront%EB%A1%9C-S3-%EC%BB%A8%ED%85%90%EC%B8%A0-%EC%A0%9C%EA%B3%B5%ED%95%98%EA%B8%B0-%E2%91%A1-CloudFront-%EB%8C%80%EC%B2%B4-%EB%8F%84%EB%A9%94%EC%9D%B8-%EA%B5%AC%EC%84%B1)  

<br><br>

# S3 + CloudFront 생성

## 1) S3 생성
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/373cdf31-d607-4898-80e3-7000c82177f3)

![image](https://github.com/PhysicksKim/TIL/assets/101965836/e5eae00d-4e91-41dc-9aa9-c25ec2eb27c6)

![image](https://github.com/PhysicksKim/TIL/assets/101965836/50f2ded6-1d73-47ca-9939-4767fb982cd0)

![image](https://github.com/PhysicksKim/TIL/assets/101965836/3a09683d-38d6-45c4-8d67-5c0b4004b4e2)

<br><br>

## 2) CloudFront 생성

![image](https://github.com/PhysicksKim/TIL/assets/101965836/01bc80b3-7174-49d2-9c32-b31d15d3380c)

![image](https://github.com/PhysicksKim/TIL/assets/101965836/12acebbc-27e0-46ce-8906-647876aaf796)

### 대체 도메인과 인증서는 차후 설정

![image](https://github.com/PhysicksKim/TIL/assets/101965836/2b0be143-56a0-4674-8490-c1c41f502d9b)  

<br><br>
  
## 3) ❗️중요! ACM 인증서 발급 

### 꼭! CloudFront 용 인증서는 버지니아 북부(us-east-1)로 생성해야 합니다

![image](https://github.com/PhysicksKim/TIL/assets/101965836/919e7b40-3c40-48d9-9e2a-ab9d6c9ea2fc)

![image](https://github.com/PhysicksKim/TIL/assets/101965836/b427d4b5-a320-4d6b-a17a-cf8f6454d334)

![image](https://github.com/PhysicksKim/TIL/assets/101965836/ed89a069-e7e7-4952-a926-9d39e9a8e834)

![image](https://github.com/PhysicksKim/TIL/assets/101965836/2788c2c9-4a95-4734-9d98-34482acc547f)  

### 위 화면에서 "Route 53에서 레코드 생성" 클릭 

## 4) 가비아에 CNAME 등록  

![image](https://github.com/PhysicksKim/TIL/assets/101965836/830329d2-0e74-46c4-bc48-aec8b663138c)

## 5) CloudFront 에서 인증서와 CNAME 수정  

![image](https://github.com/PhysicksKim/TIL/assets/101965836/db1ddfd3-b77f-4f23-80fe-3b5b27cb5392)  

![image](https://github.com/PhysicksKim/TIL/assets/101965836/166f9cc0-eddc-43b5-836e-26b5634283bd)   
  
## 완료. 확인 

![image](https://github.com/PhysicksKim/TIL/assets/101965836/142da5ba-b310-4a97-98e0-fbdfe8d4f6c7)

위 주소와 더불어 하위 경로 (ex. abc123456aqw.cloudfront.net/hello-world/index.html)로 접근시 클라우드 프론트를 거쳐서 S3 버킷에 있는 파일에 접근할 수 있음.    

## Route53 에서 레코드 생성  

> ### 이미 ec2 와 route53 과 gabia 도메인이 연결되어있다고 가정
> ![image](https://github.com/PhysicksKim/TIL/assets/101965836/e4d013bc-e6fd-4d3a-92fe-164ac7fc8eb8)
> 만약 연결되어있지 않다면 다른 글을 통해 도메인과 route53 연결 방법(dns name server) 참고하여 설정 
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/bcfb42e2-591d-44d5-9dd4-cbe2cf14a42e)  

위 설정 마치면 subdomain 으로 s3 버킷 자원에 접근 가능
  

