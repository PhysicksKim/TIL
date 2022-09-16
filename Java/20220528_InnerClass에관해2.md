> # 주의 1 ❗❗❗
> 이 글은 뇌피셜입니다 !!!  
>   
> 이 글에 적힌 내용의 정확한 근거(레퍼런스)를 아직 찾지는 못했습니다.    
> 즉, 개인적 경험에 따라서    
> "이렇게 동작하는 것 같아"  
> 라고 추측한 내용을 바탕으로 썼습니다.  

---

> # 주의 2 ❗❗❗
> 이 글은 급하게 아이디어를 옮겨적느라 말이 횡설수설입니다  
> 이 글은 아이디어가 번뜩여서 후다닥 쓴다고 말 앞뒤가 안맞고 지리멸렬하게 설명이 길어진 글입니다.  

---

# 이전글은....

왜 그렇게 해야하는지 몰랐다.  
왜 **out.new Inner**() 같은 식으로  
"**new 앞에 out은 뭐야**???"  
같은 의문만 남은채로 끝났다  
  
그런데  
정확히 Dijkstra 알고리즘을 작성하다가  
문득 뇌피셜로 이 궁금증이 풀렸다  
  
정확한 Reference는 아직 못찾았다...  
  
  
<br><br><br>  
  
# 깨닫게 된 배경  

아래 코드를 보자  

```java
// 코드 1

public class Dijk {

  public class Link {
    public Link( ... ) {
      ...
    }
  }

  
  public static void main(String[] args) {
    Link link = new Link( ... );
    ...
  }

}
```

자.  
main 메서드 안에서 저렇게 Link를 생성할 수 있을까?  
  
답은 **불가능** 이다.  
  

<br>  
  
---

<br>  

```java
// 코드 2

public class Link {
  public Link( ... ) {
    ...
  }
}

public class Dijk {

  public static void main(String[] args) {
    Link link = new Link( ... );
    ...
  }

}
```
이렇게 된다면 Link를 생성할 수 있을까?    
  
답은 **가능** 이다.  
  

<br><br>  
  
# 무슨 차이인가?  
 
내가 내린 결론(뇌피셜)은  
  
- 내가 클래스를 부르면(객체를 생성하려 하면), 그 클래스가 어딨는지 컴퓨터가 어떻게 아는가? 
- -> Static 영역에 대한 이해  
  
이다.  
  
<br><br>  
  
무슨 말인가 하면...  
  
우리가 Integer 라고 하면, java.lang에 있는 Integer임을 컴퓨터가 자동으로 인식한다  
우리가 Arrays 라고 하려면 import문에 java.util.Arrays 라고 선언해줘야지 컴퓨터가 이를 인식할 수 있다  
  
자 그럼.  
"**그 클래스가 어딨는지 컴퓨터가 알 수 있는가?**"  
가 핵심임을 명심하고  
왜 Out.new Inner() 같은 식으로 new 앞에 Out이 붙었는지 알아가보자.   
  
<br><br>  
  
# 인식할 수 있는 클래스 범위  

먼저 인식을 정의해야 할 것 같다.  
  
<br>  
  
### 인식이란  
아래 코드들을 보자  
  
```java
import java.util.StringTokenizer;
import java.util.Arrays;

class TempClass { 
  TempClass() { }
} 

public class Main {
  public static void main(String[] args) {
    Integer val = 1; // java.lang에 있어서 기본으로 Integer 클래스 인식함
    TempClass tempClass = new TempClass();      // 현재 파일에 있어서 인식함
    StringTokenizer st = new StringTokenizer(); // import를 통해 인식함  
    
    // 대표적인 Static Class인 Arrays 사용
    int[] arr = new int[] { 6,3,5,1,2 };
    Arrays.sort(arr);
  }
}
```
import를 하거나, java.lang에 있거나, 현재 .java 파일 안에 있는 클래스면  
우리가 익히 써오던 식으로 클래스를 호출하거나 생성할 수 있다.  
  
  <br>  
  
### Inner Class 생성 코드  
인식이란 개념을 갖고 Inner Class 생성 코드를 다시 보자  

```java
class Outer {
  Outer() { }

  class Inner {
    Inner() { }
  }
}

public class TestClass01 {
  public static void main(String[] args) {
    Outer out = new Outer();
    Outer.Inner inner = out.new Inner();
  }
}
```

### Q. 왜 Outer.Inner 일까?   
- A. 컴퓨터는 Outer는 인식할 수 있지만, Inner는 곧바로 인식할 수 없다  

마치 아래 그림과 같은 식이다 
![image](https://user-images.githubusercontent.com/101965836/190649680-e904ea65-ed76-48c3-af11-3e27d6550e3a.png)  
  
Outer 클래스에 대한 설계도는 Outer 적으면 바로 인식할 수 있지만  
Inner 라는건 바로 인식가능하지 않으므로, Outer를 거쳐서 들어가야 한다.  
  
<br>  
  
### 인식에 대해 살짝 정리하고 가자  
  
인식 가능한 클래스 이름들은 다음과 같다  
  
1. java.lang  
2. 현재 쓰고 있는 파일 
3. import한 클래스들   
  

<br>  

# 인식 개념 만으로는 이해되지 않는 부분이 있다! 

곧바로 인식 개념만으로는 이해되지 않는 코드를 보자  

```java
class Outer {
   static class Inner {
       static class StInInner {
           class InInInner {
               int iiiv = 100;
           }
       } 
   }
}

public class quiz7_6 {
    public static void main(String[] args) {
      
      Outer out = new Outer();
      Outer.Inner.StInInner stInInner = new Outer.Inner.StInInner();
      Outer.Inner.StInInner.InInInner inInInner = (new Outer.Inner.StInInner()).new InInInner();

    }
}
```
[InnerClass에 관해 - 1편](https://github.com/PhysicksKim/TIL/blob/main/Java/20220528_InnerClass%EC%97%90%EA%B4%80%ED%95%B4.md#%EC%B0%B8%EA%B3%A0%EB%A1%9C-static-%EC%95%88%EC%9D%98-static%EC%9D%80-%EA%B7%B8%EB%83%A5-%EB%B0%94%EB%A1%9C-new-%EC%8D%A8%EB%8F%84-%EB%90%9C%EB%8B%A4)  
에서 나온 코드이다.  
  
1편에서는   
"잘 모르겠어요..."  
하고 끝났던 코드다.  
  
잘 모른다고 했던 부분은  
  
- 왜 Static 바로 아래의 inner class(위 코드에서 InInInner) 인스턴스를 만들려면,  
new 부분에서 저렇게 이상하게 "static 객체를 만들고" 거기다가 static객체.new로 찍는거지"  

라는 내용이었다.  
  
이상한점이 한둘이 아니다  
  
1. Static 객체를 new로 만드는건 도대체 왜 가능한건데   

Static 객체?????  
기괴하다. 근데 나중에 설명하고 일단 그렇다 치자.  
또 이상한점이 있다  

2. static 객체는 그냥 new 로 만들 수 있고, static 안의 InInInner는 왜 Static 객체 만든다음 .new 해야하는건데?  
  
기이하지 않은가?  
이래서 처음에는 inner class 생성에서 맨붕이 왔다.  
하지만 오늘 깨달은 내용에 따르면  
  
**Static 영역**에 대해 이해한다면  
왜 코드를 저렇게 적는지도 이해할 수 있게 될 것이다.  
  
  <br><br><br>  
  
## Static 영역에 대한 이해  

### java 메모리 영역에 대해  
java에는  

1. Static 
2. Stack 
3. Heap 

이렇게 3가지 영역이 있다  
   
> ### 이 부분은 좀 더 강한 뇌피셜  
> 여기 아래부터는 아직 잘 모르겠는 내용들이다  
> 거의 메모용으로 적는거니까... 너무 믿지 마십쇼...
> 
> 
> 우리가 앞서말한 곧바로 인식 가능한 것들이    
> 정확히 어떤 메모리 영역에 할당되는지는 확신이 아직은 없다    
>   
> 추측하기론 static 영역 같은 느낌이겠는데    
> 래퍼런스도 없고, 아직 반례가 있는지도 깊게 생각해보지 않아서    
> "곧바로 인식 가능한 클래스는 static 영역에 담겨있습니다!!"라고 단언하지는 못하겠다.
>      
> 또는 앞서 말한 3가지 영역 말고 또 다른 영역에 담겨있을 수 있다.    
> 하지만 이 모든게 뇌피셜이니   
> 그냥 생각이 이렇구나 정도로만 참고  

<br><br>  

## Static 영역은 Instance 생성과 관련된 정보를 담지 않는다  
static 클래스는 인스턴스를 생성하지 않고 쓰는 클래스다.    
따라서 static 영역은 static 클래스를 담기 위한 영역이니까  
인스턴스 생성과 관련된 내용을 담지 않을 것이다.  
  
따라서 inner 객체를 생성하려면  
static 영역에서는 객체 생성과 관련된 정보를 담지 않고 있으니까  
Outer.Inner.StInInner 로 파고 들어가야지 생성자를 잡아낼 수 있다.    
  
또한, 잘 생각해보면  
```java
Outer.Inner.StInInner stInInner = new Outer.Inner.StInInner();
```
여기서 좌변 부분은 참조형 변수 선언에 해당하고,  
우변 부분은 객체 생성에 해당한다.  


### 1. 좌변 - 참조형 변수 부분 
먼저 좌변 참조형 변수 선언 부분을 생각해보면  
Static 영역에는 Static 클래스의 참조형 변수에 대한 정보가 없을 것이다.  
계속 말하듯 인식 영역은 static 영역을 포함하지만   
static 클래스는 객체 생성해서 쓰는 클래스가 아니니까  
static 영역은 객체 생성과 관련된 내용을 담고 있지 않다.    
  
  
그러니 곧바로 인식 가능한 영역에 있는 Outer에서  
Outer를 따라 파고 들어가야지만 Static 참조 변수 형식에 대한 정보가 있는 것이다.      
곧바로 인식 가능한 영역에 static 영역이 있기는 하지만, Static 참조형에 대한 정보는 Static 영역에 없으니까.  
  
<br><br>  
  
### 2. 우변 - 객체 생성 부분  
static 영역에는 객체 생성 메서드가 없으니까,  
Outer.Inner.StInInner() 까지 파고들어가야지 객체 생성 메서드가 있는 것이다.  
  

<br>  

이렇게 설명해두면...  
이제 준비가 끝났다  
  
<br><br><br>  
  
```java
Outer.Inner.StInInner stInInner = new Outer.Inner.StInInner();
Outer.Inner.StInInner.InInInner inInInner = (new Outer.Inner.StInInner()).new InInInner();
```
    
1. Static 객체를 new로 만드는건 도대체 왜 가능한건데   
2. static 객체는 그냥 new 로 만들 수 있고, static 안의 InInInner는 왜 Static 객체 만든다음 .new 해야하는건데?  
  
이에 대해 다시 답을 정리하고 끝마치자    
  
  <br><br>
  
## 1. static 객체를 만드는 이유
static 영역에는 객체 생성과 관련된 내용이 없기 때문에  
InInInner 객체 생성을 위해서는 객체 생성과 관련된 내용을 끌어와야 하기 때문에  
static 클래스 객체를 만들어야 한다  
  
## 2. static 아래 inner class 생성에는 왜 또 앞에 (뭐뭐뭐).new 로 가는건데  
앞서본 static 객체 만드는 부분에서 new 앞에 뭐가 안붙는 이유는  
static 클래스들은 인식 가능 영역에 일단 올라가기 때문이다.  
이는 static 클래스의 생성자 까지는 올라가지만, 참조형 까지는 올라가지 않기 때문인 것 같다.  
  
한편, non-static인 Inner에 대해서는    
곧바로 인식 가능한 영역에  
inner 클래스 객체 생성에 대한 정보가 없다.  
따라서 객체 생성에 대한 정보를 누군가가 갖고 있어야 하는데  
static 영역도 이를 갖고 있지 않고,  
곧바로 인식 어쩌고저쩌고 영역도 inner 클래스 생성에 대한 정보는 갖고있지 않다.  
  
따라서 객체를 생성해서 아랫것들 생성에 대한 정보를 갖고오도록   
접근책을 뚫어 놔야지 인식 되는거다  
  
마치 Outer.Inner.InInner 처럼  되는 것이다  
  
<br>  
  
### 아니 그러면 파일 바로 아래에 있는 class는 왜 곧바로 인식이 되냐?  
```java
class Outer {
}

public class Test {
    public static void main(String[] args) {
        Outer out = new Outer();
    }
}
```
저기 있는 Outer는  
파일과 바로 맞닿아 있는 class기 때문에   
곧바로 인식 가능한 영역에 포함된다.  
