# 클래스 로더 작동방식에서 답을 얻음

[참고한 글 - Does the Java ClassLoader load inner classes?](https://stackoverflow.com/questions/24538509/does-the-java-classloader-load-inner-classes)  
  
```java
package kuporific;

public class A {
    private static class B {}
    private class C {}
}
```

자 위의 코드를 컴파일하면 "몇 개의 .class"파일이 생성될까?  
컴파일하면 class 파일이 생성된다 라는 사실을 모르면, java 컴파일에 대해 잠깐 알아보고 오도록 하자.  
  
신기하게도 3개의 파일이 컴파일된다  
```
├── META-INF
│   └── MANIFEST.MF
└── kuporific
    ├── A$B.class
    ├── A$C.class
    └── A.class
```  
  
오...  
나는 A.class 파일만 생기고, 그 파일안에 inner 클래스들이 담겨있을 줄 알았는데  
  
### 사실은 컴파일하고나면 Inner class 란건 존재하지 않는다.   
  
이점이 눈이 뜨이게 해준 부분이다.  

그래서 이런 생각이 들었다.  

### 아 그러면, 이전에 InnerClass에 대한 고민들이, class 파일이 분리되었기 때문에 나온거구나!!!  
그런것 같다  

---

# 문제의 코드의 class 파일을 까보자  

```java  
public class A {
    static class B {
        static class C {
            class D {}
        }
    }
}
```

이걸 컴파일하면 아래와 같이  
<img width="504" alt="image" src="https://user-images.githubusercontent.com/101965836/207548557-86f19a79-917d-4fd7-8fa1-c8c80b9ea46f.png">  

```
├── A.class
├── A$B.class
├── A$B$C.class
└── A$B$C$D.class
```
이렇게 class 파일이 생기는 것을 볼 수 있다.  
   
### 여기서 앞선 InnerClass 문제를 다시보자  

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

핵심은
왜 static이 아닌 inner class는 new 앞에 뭐가 붙고   
static inner class는 new 앞에 안붙는가 이다.  
  
여기서부턴 일단 추측인데  
문서 처음에 걸어둔 링크를 참고하면    
A$B.class 같은 파일은 로드되지 않고  
Main.class 와 A.class 파일만 우선 로드됨을 알 수 있다.  
그러면 아마 클래스 로드와 생성자, 그리고 static 클래스의 로드 타임과 관련이 있지 않을까 추측해본다.  
