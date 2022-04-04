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

파일이 추가 됐다고 해보자
```
$ cat hello1.txt
1
```
여기서 git에 commit 하려면
```
git add hello1.txt
git commit -m "Message 1"
```
으로 하면 된다.  
  
파일 수정된 내용을 커밋할 때는, 위와 똑같이 해도 되지만 commit에 -am 으로 할 수 있다.  
```
git commit -am "Message 2"
```
   
## add의 의미
git은 자동으로 디렉토리 내의 모든 파일을 관리하지는 않는다.  
따라서 "이 파일도 관리 할꺼야" 라는 의미로 add로 파일을 추가해주는 것이다.  
   
   
   
# 5. 여러 개의 파일을 하나의 버전으로 만들기  
add로 관리할 파일을 추가하면 된다.
```
git add hello2.txt
```
  
앞서 add의 의미에 대해 더 자세히 보자.  
  
![image](https://user-images.githubusercontent.com/101965836/161496021-58a65b01-2454-4579-9c31-faf002b011ab.png)  

Repository : 깃에서 버전이 저장되는 곳  
Working tree : 우리가 파일을 만들고 삭제하는 작업 공간  
Staging Area : 깃이 working tree에서 어떤 파일들의 버전을 관리할 지 다루는 곳. 즉 working tree에 파일이 존재한다고 끝나는 게 아니라 Staging Area에 해당 파일이 등록되어 있어야 git에 의해 버전이 관리가 된다. 또 파일 관리 사항을 staging area위에 올린다는 의미로 stage 라는 동사를 쓴다.   
  
  
# 6. 버전간의 차이점 비교  
  
```
git diff
```
마지막 버전과 working tree 사이에서 어떤 점이 바뀌었는지 알 수 있다.  
  
  
```
git log
```
commit된 로그들을 보여준다.  

```
git log -p
```
버전별 바뀐 내용을 보여준다.  
  
  
# 7. checkout과 시간여행
과거 버전으로 돌아가고 싶을 때 checkout을 쓴다.
```
git checkout (돌아갈 commit 버전 코드) 
```
![image](https://user-images.githubusercontent.com/101965836/161499289-89afdea5-171f-4162-96ae-effbaf9d1665.png)  
빨간 밑줄로 표시한 부분이 해당 commit의 버전 코드다.  

다시 최신 상태로 돌아가려면 
```
git checkout master
```
라고 하면 된다. (마스터 브랜치로 돌아간다는 뜻)   
  
  
# 8. 보충수업

## commit의 -am 옵션
```
git commit -am "Message 3"
```
-am을 하게 되면 add와 commit이 같이 된다.  

## 여러줄로 commit 메세지 쓰기
```
git commit
```
그냥 commit 까지만 적으면 텍스트 에디터가 뜬다. 텍스트 에디터를 이용해서 여러 줄로 commit message를 적을 수 있게된다. 이 때 기본 에디터가 자동으로 뜨므로, 기본 에디터를 바꾸고 싶다면 어떻게 git의 기본 에디터를 바꾸는지 검색해서 적용하면 된다.  
  
  
# 9. 삭제
git reset
```
git reset --hard (버전명)
```
(버전명) 으로 리셋 하겠다  
**헷갈림 주의**  
해당 (버전명)으로 가겠다. 되돌리겠다. 는 뜻.  
   
   
# 10. git revert
reset과 굉장히 비슷하기 때문에 헷갈림 주의  
reset은 특정 버전을 지우는 것과 다름 없기 때문에 revert를 이용해서 버전 변화를 남겨둔채로 이전 버전으로 돌아가는 것이다.  
마치 역함수처럼 변화를 상쇄시키는 방식으로 작동한다.  

```
git revert (버전명)
```
앞서 **역함수** 처럼 동작한다 했다.

따라서 버전 1,2,3,4 가 있고, 현재 4인데 3으로 돌아가고 싶다고 해보자.  
그러면 (버전명)에 3을 쓰는게 아니라, 4버전을 써야한다.  
역함수 처럼 동작하니까 4의 반대 행동을 하겠다는 뜻으로 git revert 4 라고 해야한다.   
  
  
  
# 10. 수업을 마치며
이후 볼만한 주제들  
  
## diff tool
버전 차이를 정교하게 볼 수 있다.

## .gitignore
버전 관리에서 예외로 두고싶은 파일을 두는 곳 (비밀번호 저장한 파일 등)

## branch
  
## commit id 대신 tag
commit id 뿐만 아니라 이름을 붙여서 주요한 이름을 찾을 수 있다.  

## backup
자체적으로 안전한 backup 장치(github)를 사용할 수 있다.  
