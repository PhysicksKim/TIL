
[참고1 - static classes in java](https://stackoverflow.com/questions/7486012/static-classes-in-java)   
[참고2 - why cant we have static outer classes](https://stackoverflow.com/questions/18036458/why-cant-we-have-static-outer-classes)   
[참고3 - JVM 내부 구조 & 메모리 영역](https://inpa.tistory.com/entry/JAVA-%E2%98%95-JVM-%EB%82%B4%EB%B6%80-%EA%B5%AC%EC%A1%B0-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%98%81%EC%97%AD-%EC%8B%AC%ED%99%94%ED%8E%B8)  
[참고4 - java inner class and static nested class](https://stackoverflow.com/questions/70324/java-inner-class-and-static-nested-class)    
[참고5 - When exactly is it leak safe to use (anonymous) inner classes?](https://stackoverflow.com/questions/10864853/when-exactly-is-it-leak-safe-to-use-anonymous-inner-classes)  
   
<br><br>  

# 선 요약  
1. static class 는 **inner 에서만 가능**하다. outer class는 어차피 static class와 동일하게 동작한다.     
2. non-static inner class 객체는 outer \<-\> inner **참조를 갖고 있다**   
따라서 Garbage Collection이 제대로 안이뤄져서 **메모리 낭비가 발생**한다.  
그래서 **Inner Class는 Static으로 선언**해야한다.    
  
<br><br>
  
---
  
<br><br>
  
# static 키워드는 클래스, 변수, 메서드와 다름  
이거 때문에 처음에 많이 헷갈렸다.   
변수와 메서드에 static이 달리면  
동작 자체는 심플하다.  
  
> 변수와 메서드에 static이 달리면   
> **객체보다 먼저 메모리에 올라간다**   
> 변수는 공통된 하나의 값으로 다룬다   
    
따라서 변수와 메서드에 달린 static은 처음부터 어느정도 이해했다  
  
<br>  

### static 필드 , 메서드 추가 설명  
  
- static 필드 : 객체들에서 통일된 값을 유지해야 할 때 사용한다     
- static 메서드 : 객체 생성 없이 호출되어야 하는 경우    
  
> ### static 메서드  
> 1. 유틸리티, 헬퍼  
> 2. Factory 메서드  
> 3. 성능최적화 (호출이 잦지만 객체 생성이 필요 없는 경우)  
> 4. 상태가 없는 메서드 (메서드가 입력된 인자에만 의존)    
  
<br><br> 
  
# static 클래스  
  
static 메서드, static 필드는 어느정도 감이 잡히는데  
**static 클래스**는 감이 안잡힌다.    

<br>  

## 감이 안잡히는 이유  
  
> static : 객체보다 먼저 메모리에 올라간다
  
class 자체가 객체보다 먼저 메모리에 올라간다고?  
뭐? 모르겠다.  
  
> ### 처음에 한 오해      
> 아 그러면,  
> static class는 해당 class의 내부 멤버들이 전부 static으로 취급된다는건가?  
> 그래서 객체 생성 해봤자, 어차피 싱글톤처럼 동작하는건가?  
  
<br> 
  
## class에 달린 static의 동작 방식 

### 1. static이 없으면, Outer 객체에 참조가 포함된다  
static이 없으면, Inner 객체가 Outer 객체에 대한 참조를 암시적으로 갖게 된다.    
따라서 Outer - Inner 참조를 갖게 된다    
   
- Outer 참조를 갖고 있으므로   
inner 객체가 outer 객체의 non-static field에 접근할 수 있다  
  
<br>  
  
### 2. static이 붙이면, 별도로 class 파일 두는것과 동일하다  
static이 있으면, Outer 객체와 Inner 객체간의 암시적인 참조가 없다   
   
- Outer 참조가 없으므로   
inner 객체가 outer 객체의 non-static field에 접근할 수 없다   
   
<br><br>  
  
## 동작 방식 때문에, static으로 메모리 누수를 막는다  
  
따라서 inner class가 굳이 outer 객체의 필드를 사용할 필요가 없다면  
static으로 선언하기를 권장한다.   
  
<br><br>

---

<br><br>  

# 메모리 누수 되는 이유  

## 1. Non-static인 경우  

### 1) main() 에서 Outer 객체 생성 (참조가 생김)    
![image](https://github.com/PhysicksKim/TIL/assets/101965836/64906c08-3f4d-4990-a67d-d65441c43486)  

### 2) main() -\> Outer 객체 참조가 끊어짐  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/85fda0e9-c2cc-409d-9416-07ab6c963508)  
  
가비지 컬랙터는 나(객체)를 참조하는 것이 없을 때   
가비지 컬랙션이 이뤄진다.  
  
하지만   
위 그림과 같이 된 경우  
Outer 객체도 Inner 객체를 참조를 가지고 있고    
Inner 객체도 Outer 객체 참조를 갖고 있다.    
  
즉, **서로 참조를 갖고 있는데**,
이는 일반적으로 메모리 누수의 원인이 된다고 한다.   
   
<br><br>  

## 2. static인 경우  

### 1) main() 에서 Outer 객체를 참조  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/f59af6b3-836f-4f2e-b167-d9e8a4faec4e)  

### 2) main() -\> Outer 객체 참조가 끊어짐   
![image](https://github.com/PhysicksKim/TIL/assets/101965836/330dd45e-05e4-4026-8fe2-f08cea93db94)  
   
### 3) Outer에 대해 가비지 컬랙션 이뤄짐  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/3f74ae6b-0d50-44f0-9f1c-c8144d47b0bb)
  
### 4) Inner도 가비지 컬랙션 이뤄짐  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/e023d8f5-03e3-4d05-9863-99a9097d3903)  
  
<br><br>  

### 따라서, 가능하면 Static Inner Class를 쓰자   
Outer 객체의 필드를 사용할 일 없으면  
Static Inner Class를 사용해서 메모리 누수 원인을 없애자   
  
