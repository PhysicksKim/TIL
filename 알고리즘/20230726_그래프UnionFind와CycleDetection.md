관련 문제   
- [\[20040번: 사이클 게임\]](https://www.acmicpc.net/problem/20040)  

### 목차  
1. 그래프에서 cycle 개념
2. Cycle Detection 이해를 위한 기본개념   
3. Cycle Detection 이해  
  
# 그래프에서 Cycle 개념    

![image](https://github.com/PhysicksKim/TIL/assets/101965836/35e28145-eb67-4070-a243-da267100a39b)  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/93fe15be-38fe-4b7b-82f5-3021e9dfeb4c)  
    
![image](https://github.com/PhysicksKim/TIL/assets/101965836/0ad1fe76-a01b-46cb-809e-b538aa144dac)  
이렇게 다시 처음 노드로 돌아올 수 있는 길이 있다.  
  
> ### 실용적 의미
> 어떤 값이 전파될 때(ex. 네트워크 브로드캐스팅) 그래프에 Cycle이 있으면   
> 무한 loop 가 생기는 위험이 있다. 이를 감지하기 위해 Cycle 개념이 필요.  

<br><br><br>  

---

<br><br><br>  
   
# Cycle Detection 이해를 위한 기본개념   
    
Cycle Detection 알고리즘을 이해하기 위해서는     
   
1. 서로소 집합 이 뭔지 알고    
2. 자료 구조에서 집합을 어떻게 표현하며    
3. 두 집합을 합치고(union), 각각 두 노드가 같은 집합인지 알아보는(find) 방법   
  
을 이해해야 한다.   
  

: 서로소집합(Disjoint Set) - 합집합-찾기(union–find)   

<br>  
  
## 1. 서로소 집합 개념  

---

<br>

![image](https://github.com/PhysicksKim/TIL/assets/101965836/b21675b9-5c88-472f-849f-6d59d13ef3d9)    

<br>
   
---

<br>
   
![image](https://github.com/PhysicksKim/TIL/assets/101965836/3693a950-c8c7-4368-a55f-ce17daeb1e9b)   

<br>
  
---
  
<br><br><br>

## 2. 자료 구조상에서 집합 표현  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/0887cec2-6209-47f9-ba91-345782a8102a)  

각 node는 parent 포인터를 가지고 있고  
parent를 타고 올라가면 root에 도착한다.  
이는 모든 node가 같은 root로 수렴하게 되므로 같은 집합임을 판단할 수 있다.  
    
> "그러면 parent 말고 root을 가리키면 되지 않나?"    
> 라는 개념이 "Path Compression ; 경로 압축" 개념이다   
    
> "parent 말고 root을 다 가리키면 되지 않나?"    
> 그래도 된다. 근데 매번 root을 가리키도록 업데이트 체크 로직이 필요해서 조금 동작이 비효율적이게 될 수 있다.
> Path Compression , Union by Rank 를 참고  

  
---

<br>

![image](https://github.com/PhysicksKim/TIL/assets/101965836/c5b3e0ba-787f-460b-9034-977873a5785d)  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/32775289-b3a6-4235-bb36-be773633b1b7)
  
위에서 find(3); find(2) 같은 명령으로 해당 노드가 어느 집합인지(root이 어딘지)를 판별할 수 있다    
  
<br>  
  
---  

<br><br><br>
  

## 2. 같은 집합 체크(find) - 합집합(union)  
  

### a) 두 노드가 같은 집합인지 체크(find)  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/6e7bc78f-c511-4884-b8c7-c03f7f6fc760)  
  
비교할 두 node에 find(index) 명령으로 root을 찾고 비교하면 된다.  
root이 같으면 같은 집합이다.  

<br><br>
  
### c) 합집합(union)  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/f31c0d58-680b-4feb-beb8-f7af01ccb182)  
  
두 집합을 합치려면   
한쪽 집합이 다른쪽 집합의 node를 parent로 가리키면 된다.  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/bf6f166a-8fa9-4060-a1a9-0b1e7c72e4e7)  
다른쪽 집합의 어느 node를 가리켜도 어차피 root은 동일하게 수렴한다.  


<br><br><br>  

---

<br><br><br>  

# Cycle Detection  
  
앞서 **a) 두 노드가 같은 집합인지 체크(find)** 를 사용하면 된다.  

### Cycle Detection 방법    
노드 a 와 b를 이어주는 간선 edge(a,b)가 주어졌을 때  
find(a) find(b) 를 해본다.  
find(a) == find(b) 인 경우 (즉, 같은 부모를 가리키면)     
Cycle을 이루게 된다.   
    
<br>    
  
### 예시  
     
![image](https://github.com/PhysicksKim/TIL/assets/101965836/29b731ec-5486-43d1-b452-27059a1b1ef5)   
    
위 그림과 같이    
노드 1 2 3 이 하나로 이어져 있다고 해보자.    
    
<br>   
  
---  
  
<br>  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/8042cd93-2ad7-44dc-90a7-c08f869d61bf)   
  
여기에 노드 2와 3을 이어주는 엣지가 주어졌을 때,      
edge(2,3) 가 추가되면 Cycle 이 생길지 Detection을 해보자.  
    
1. edge(2,3) 노드 2와 노드3 에 대해서 find(2) find(3) 을 해본다.    
2. parent\[] 배열로 root을 타고 들어가면 **"find(2) 결과 1"** 이고 **"find(3) 결과 1"** 이다   
3. 따라서 edge(2,3) 을 추가하면 "같은 집합에 선을 하나 추가하는 것 이므로" cycle 이 생긴다.   
    
