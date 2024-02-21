# 문제  

```
$ sudo yum install certbot
Loaded plugins: product-id, search-disabled-repos, subscription-manager
No package certbot available.
Error: Nothing to do
```
  
certbot 을 못찾는 다는 오류 메세지가 떴다.      
  
# 해결법  
   
```
sudo amazon-linux-extras install epel
```
   
epel 저장소를 추가로 설치해주면 된다   
  
<br><br>  

# epel 이란   
  
EPEL (Extra Packages for Enterprise Linux)  
  
기본 탑제되지 않은 추가 패키지들을 다운로드 할 수 있는 **"추가적 무료 리눅스 패키지 저장소"** 이다  
  
