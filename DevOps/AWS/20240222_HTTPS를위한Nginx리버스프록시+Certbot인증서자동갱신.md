# 중요! Amazon Linux 2 환경임  
### Ubuntu 가 아닙니다!

## 비교 1. 패키지 저장소  
ubuntu 의 경우 <code>apt</code> 를 통해 설치하고, amazon linux 2 는 <code>yum</code> 을 통해서 설치합니다.  

## 비교 2. 패키지 명이 다름  
ubuntu certbot 예시를 보면 <code>python3-certbot-nginx</code> 로 설치하는데  
Amazon Linux 2 에서는 <code>certbot-nginx</code> 로 설치합니다.  

<br><br><br>  

---  

## Certbot nginx 플러그인 설치 

[참고링크](https://flamingotiger.github.io/backend/devOps/aws-linux2-certbot-nginx/)  
  
### 발생한 문제  

```
No package certbot available.
```

```
No package python3-certbot-nginx available
```

위와 같은 문제는 
a. epel 저장소가 있어야 certbot 설치 가능 
b. 패키지 명이 다름. ubuntu(apt) 에서는 python3-certbot-nginx / amazon linux 2(yum) 에서는 certbot-nginx   

  <br>  
  
### 1) epel 저장소 
[epel 참고](DevOps/AWS/20240222_awsLinux2에서certbot찾지못하는문제.md)  
  
```
sudo amazon-linux-extras install epel
```

### 2) certbot , certbot-nginx 설치  

```
sudo yum install -y certbot
sudo yum install certbot-nginx
```

> certbot 뿐만 아니라 리버스 프록시로 쓸 nginx 에 맞추기 위해서 certbot-nginx 플러그인도 설치해야 한다.  

<br><br>  

# Certbot 인증서 발급 

```
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

여러 도메인을 사용하려면 위와 같이 <code>-d</code> 를 연속으로 사용하면 된다.  
  
### 예시  

```
sudo certbot --nginx -d physickskim.site
```

## 인증서 확인 방법 

인증서 파일이 진짜로 있는지 확인해 보고 싶으면 <code>cd</code> 명령으로는 접근할 수 없다.  
권한 문제로 인해서 <code>cd</code> 사용 시 denied 된다  
  
대신 <code>sudo ls</code> 로 경로에 존재하는 파일들을 확인해볼 수 있다.  
  
```
sudo ls /etc/letsencrypt/live/yourdomain.com
```
  
또는 아래와 같이 권한을 얻은 다음 cd 할 수도 있다.  
  
```
sudo -i
cd ...경로...
```
  
> cd 명령은 sudo 를 쓸 수 없다. cd 는 쉘 내장 명령이라서 유저 권한과 무관하다고 한다.
  
  
<br><br>  

# Nginx 리버스 프록시 설정  

HTTPS 적용을 위한 nginx 리버스 프록시 설정  

> ### 왜 리버스 프록시?
> spring App이 직접 https용 인증서를 file path 로 참조하는 것 보다
> nginx 가 https 를 처리하는 구조가 더 좋다.  
    
```
server {
    listen 80;
    server_name 도메인주소;

    # HTTP 요청을 HTTPS로 리다이렉트
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl;
    server_name 도메인주소;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    location / {
        proxy_pass http://localhost:8080; # Spring 애플리케이션으로 요청 전달
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
  
도메인주소 부분을 본인의 도메인 주소로 변경해주면 된다.  
도메인주소 뒤에 꼭 <code>;</code> 를 붙여야 함을 명심하자.  
  
<br><br>  

# Certbot 자동 발급  

> Amazon Linux 2 기준입니다

## 자동 발급 타이머 실행 
  
```
sudo systemctl enable certbot-renew.timer
sudo systemctl start certbot-renew.timer
```

타이머 활성화 이후 타이머를 시작합니다.  
타이머 활성화&시작 여부는 아래 명령을 통해 확인할 수 있습니다.  

```
systemctl list-timers | grep certbot
```

<br><br>  
  
## 제대로 설정 됐는지 체크  

```
sudo certbot renew --dry-run
```
