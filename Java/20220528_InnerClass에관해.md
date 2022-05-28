# InnerClass란? 
내부 클래스(InnerClass)는 말 그대로 클래스 안에 있는 클래스이다.  

```java
class Outer {
    class Inner {
        int iv = 100;
    }
}
```

# Why InnerClass?  
InnerClass를 쓰는 이유는 아래와 같다  

<img width="282" alt="image" src="https://user-images.githubusercontent.com/101965836/170825234-bf2c9bd2-dddf-4940-b920-f2d3700df688.png">  

1. **내**부 **클래**스에서 **외**부 **클래**스의 **멤버**에 **손쉽**게 **접근**할 수 있게 됩니다.  
2. 서로 **관**련 있는 **클래**스를 **논리**적으로 **묶어**서 **표현**함으로써, **코**드의 **캡슐**화를 **증가**시킵니다.  
3. **외**부에서는 **내**부 **클래**스에 **접근**할 수 **없으**므로, **코드**의 **복잡**성을 **줄**일 수 있습니다.  
  
  
# 근데 이거 생성이 복잡하다
다음 중 어떤 방법으로 inner 인스턴스를 생성할 수 있을까?  
```java
// 1.
Outer.Inner inner = new Outer.Inner();

// 2. 
Outer out = new Outer();
Outer.Inner inner = new out.Inner();

// 3.
Outer out = new Outer();
Outer.Inner inner = out.new Inner();
```

**정답은 3번**이다  
  
### 근데 out.new 이거 대체 뭐지?  
new는 + - * / 같은 연산자라고 생각했는데  
out. 이 앞에 온다   
  
new가 heap에서 객체를 생성할 공간을 확보하고 참조값을 return 해주는 연산자라는 정도는 알고있다   
근데 out. 이 앞에 붙는건 뭐지?  
왜 마치 out의 메서드처럼 out.new 라고 적는걸까?   
   
  
# 다른 예시도 보자
```java
class Outer2 {
    static class Inner2 {
        class InInner {
            int iv = 10;
        }
    }
}

public class quiz7_6 {
    public static void main(String[] args) {
    
      Outer2 out2 = new Outer2();
      Outer2.Inner2 inner2 = new Outer2.Inner2();
      
      Outer2.Inner2.InInner inInner = inner2.new InInner();
      // Outer2.Inner2.InInner inInner = new InInner(); // 안됨
      
    }
}
```
희안하다...  
static 클래스를 객체로 만든다음 .new로 불러야 한다  
  
  
## 참고로 static 안의 static은 그냥 바로 new 써도 된다  
```java
class Outer3 {
   static class Inner3 {
       static class StInInner {
           class InInInner {
               int iiiv = 100;
           }
       } 
   }
}

public class quiz7_6 {
    public static void main(String[] args) {
      
      Outer3 out3 = new Outer3();
      Outer3.Inner3.StInInner stInInner = new Outer3.Inner3.StInInner();
      Outer3.Inner3.StInInner.InInInner inInInner = (new Outer3.Inner3.StInInner()).new InInInner();

    }
}
```
  
  
# 아직 왜 이런지 잘 모르겠다
아니    
static이면 그냥 Method Area(=Static Area)에 그냥    
처음 실행될 때부터 메모리상에 딱 정의되는 거 아닌가?    
그러면 static 바로 아래는 객체 따로 생성하지 않아도 바로 접근할 수 있어야 하지 않나? 마치 객체가 처음에 바로 생성된 것 처럼  
근데 딱 유일하게 new는 바로 접근안되고 static이더라도 객체 만들고 접근해야하네?  
위에서 inInInner 만들 때 처럼  
  
일단 규칙을 보니까   
1. 가장 바깥에 있는 Outer 클래스는, 그냥 무조건 new로 인스턴스 생성 가능    
2. 그 안에 static - static - ... 계속 static으로 이어지면 그냥 new로 생성 가능    
3. static이 계속 이어지다가 한번이라도 NON-static class가 나오면 무조건 그거 바로 위에 객체 생성하고 (new Outer.Inner.InInner()).new InInInner 이렇게 해줘야함  
    
이렇게 동작하는 것 같다  
근데 이게 왜 이런지 도대체 모르겠다  
new라는게 Outer 말고는 객체를 만들어야지만 가능한 그런건가?   
  


