CLI 환경에서 GIT의 정보를 백업하는 방법

# 1. 수업소개

# 6. 원격저장소와 연결
```
git remote add (원격 저장소 주소)
```
![image](https://user-images.githubusercontent.com/101965836/161505229-91c8eedf-94ee-4d47-bdb1-1a62623fac1f.png)  
꼭! HTTPS로 체크하고 저장소 주소를 복사해야한다.  
  
예시
```
git remote add origin https://github.com/egoing2/my-repo.git
git push -u origin master
```

```
git remote -v
```
원격 저장소 연결된 상태를 볼 수 있다.

# 7. push
[참고](https://ebbnflow.tistory.com/198?category=842626)  

이제 
```
git add 파일
git commit -am "메세지"
git push
```
로 업로드 할 수 있다.  
  
# 8. 복제
백업을 했으면 복원을 할 수 있어야 한다.  
```
git clone (저장소주소)
```
```
git clone https://github.com/PhysicksKim/codinglab.git
```

# 9. pull
```
git pull
```

# 10. 오픈소스
git도 오픈소스로 되어있다. 라는 내용  
  
# 11. 수업을 마치며  
## SSH
원격 저장소와 통신할 때마다 인증을 물어보는 것이 귀찮다면 SSH를 이용하면 자동으로 로그인 할 수 있다.  
  
## issue tracker
프로젝트 진행하며 생기는 이슈를 공유하는 게시판. 일종의 to-do list.  
  
