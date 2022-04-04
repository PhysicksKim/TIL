# GIT2 - CLI버전관리 
# 1.수업소개
오리지널 깃(git bash)을 이용해 버전관리하는 법을 배운다. 이는 GUI환경을 이용할 수 없는 서버와 같은 경우에도 사용할 수 있기에 개발자에게 매우 중요하다.  
  
# 2. git 설치 

# 3. CLI 버전관리의 시작

## git init .
**"git에게 현재 디렉토리에 대해 관리를 시작하겠다"** 라고 하는 명령어 이다.  
![image](https://user-images.githubusercontent.com/101965836/161489994-ad365e60-e25f-40c8-85de-0aebed6755f6.png)  
  
```
KHC@DESKTOP-TNMP2Q4 MINGW64 /g/MyHistory/Learning/GIT2_version
$ git init .
Initialized empty Git repository in G:/MyHistory/Learning/GIT2_version/.git/

KHC@DESKTOP-TNMP2Q4 MINGW64 /g/MyHistory/Learning/GIT2_version (main)
$ ls -al
total 8
drwxr-xr-x 1 KHC 197121 0 Apr  4 15:58 ./
drwxr-xr-x 1 KHC 197121 0 Apr  4 15:57 ../
drwxr-xr-x 1 KHC 197121 0 Apr  4 15:58 .git/
```
init을 하면 위 사진과 같이 .git 이라는 디렉토리가 생긴다. 
.git 폴더에다가 버전이 어떻게 변화해왔는지를 기록한다.   
  
  
# 4. 버전 관리 만들기

