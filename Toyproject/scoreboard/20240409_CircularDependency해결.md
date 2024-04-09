# Circular Dependency
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/7a9de86a-8fe8-48ae-bcb1-f4e7fc858a41)  
  
서로가 서로를 의존하게 되는 문제를 말한다.  
  
에이 바보도 아니고 누가 그러겠어 했는데  
급하게 만들다 보니 구조를 잘 못 짜서 Circular Dependency 가 생겼다.  
  
우선 급하게 구현해둔 다음 이번에 Refactoring 했다.  

<br><br>  

# 이전 의존성 구조  

![image](https://github.com/PhysicksKim/TIL/assets/101965836/0eaf67c7-a095-4d42-9ca5-07d41419d35f)  
   
원래는 RemoteCodeService 만 구현하려 했다가   
뒤늦게 AutoRemote 를 추가하다 보니까 위와 같이 Circular Dependency 가 발생했다.  
  
가장 큰 원인은 Domain Root 으로 비즈니스로직을 끌어올려야 하는데  
AutoRemote 가 Domain Root 역할을 하면서 꼬이기 시작한 게 문제였다.  
  
   
<br><br>  
  
# 코드 개선   
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/c1d46fdd-87d2-4047-b1b3-97ba57bc7b17)  
  
둘로 나뉜 도메인을 SocreBoardRemoteService 로 하나로 만들었다.  
  
그리고 SocreBoardRemoteService 에서 모든 비즈니스로직에 따른 분기 처리를 담당하도록 했다.  
