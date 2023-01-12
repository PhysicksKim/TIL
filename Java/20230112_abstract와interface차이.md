# 이 문서는 책을 기반으로 한 뇌피셜입니다.  
헤드 퍼스트 디자인 패턴, 객체지향 프로그래밍\[김동현\] 같은 책들을 읽은 뒤  
Abstract 와 Interface 차이가 뭐지? 라는 의문에   
저 나름대로 답을 정리해놓은 글입니다.  
따라서 틀렸을 수 있습니다.  
    
내가 이해한 객체지향은 이렇다.  
내가 이해한 abstract 는 이렇게 쓰인다.  
라는 정도로 읽어주세요.  

### 글이 길다면, 제일 마지막에 "정리" 챕터만 읽어도 됩니다  
  
---
  
# Abstract 와 Interface 차이  
  
Abstract Class 와 Interface 는  
아주 간단히 형태로만 놓고 봤을때 뭔 차이인지 알 수 없다.  
이 때문에 오히려, "그냥 전부 interface 로 쓰면 되는데 왜 Abstract Class가 있지?" 라는 의문이 들었다.  
  
### 차이를 모르겠어!  

- abstract class 로 만든 Animal    

```java
public abstract class Animal {
  public abstract void speak();
}
```

- interface 로 만든 Animal     

```java
public interface Animal {
  public void speak();
}
```
  
위 코드를 보자.   
차이가 있는가? 없다.  
  
그럼 아래 코드를 보자  
  
### 차이를 모르겠어 (2)  

- abstract class 를 구현(상속)  

```java
public class Dog extends Animal{
    @Override
    public void speak() {
        System.out.println("BARK!");
    }
}
```

- interface 구현  

```java
public class Dog implements Animal {
    @Override
    public void speak() {
        System.out.println("BARK!");
    }
}
```
  
두 코드에 차이가 있는가?  
없다. 똑같다.  
  
<br>  

---
  
## 그러면 차이가 없나?  
  
그러니까 말이다! 아니 차이가 없어 보이는데 왜 abstract랑 interface를 나눠둔거야?    
그럼 이 사태의 근원부터 차근히 따져보자.  
  
### 뭔가 이유가 있겠는데...  
  
간단히 말하자면, 기능적으로는 abstract 가 없어도 되지만  
코드의 전체적인 설계 차원에서 abstract를 따로 만들어 둔거다.  
  
기능적으로 라는 말은  
abstract 말고 interface 를 쓰더라도  
동일하게 구현할 수 있다.  
  
### 어? Abstract 랑 Interface 기능상 차이도 있잖아?  
  
Abstract Class 는 필드를 만들 수 있고, 온전한 메서드도 만들 수 있다.  
```java
public abstract class Animal {
    private String name;
    public String getName() {
        return name;
    }
  
    public abstract void speak();
}
```
하지만 Interface는 위처럼 name 필드를 갖거나, 온전한 메서드를 가질 수 없다.   
따라서 기능적으로 차이가 있다는 말은 맞다.  
  
### 기능적 차이가 있다.  
  
- 오버라이딩 사용해서 abstract 흉내  

```java
public class Animal {
  
  private String name;
  
  public String getName() {
    return name;
  }
  
  public void speak() {
    System.out.println("You Have To Do OVERRIDING speak() method!!");
  }
}
```
abstract method가 자식에게 구현을 강제했던 것과는 다르다   
위의 Animal은 오버라이딩을 통해서 speak() 메서드의 다형성을 챙기는 식이다  
  
하지만, 실수로 어떤 개발자가 Animal을 상속했지만 speak() 오버라이딩을 깜빡했다면  
이를 Java 언어 규칙상으로 강제하지는 못하고,  
위처럼 "speak() 오버라이딩 해야합니다" 라고 메세지만 띄울 수 있다.  
  
따라서 기능적으로 abstract 가 갖는 의미도 당연히 있다.  
  
- 인터페이스 사용해서 abstract 흉내  

```java
public class Animal {
  
  private String name;
  
  public String getName() {
    return name;
  }
}

public class Cat extends Animal implements Speakable {
  
  public void speak() {
    System.out.println("moew");
  }
}
```
이렇게 Cat Dog Cow 같은 애들이 그냥 Speakable을 다들 구현하도록 해도 된다.  
하지만 이러면 어떤 개발자가 까먹고 Speakable 을 구현 안할 수 있다.  
역시나 Abstract 의 공백을 매울수는 없다.  
  
### 따라서, Abstract 는 존재 의미가 기능 그 자체에도 있다.  
공통적으로 나오는 행동이지만  
구체적인 행동은 자식마다 좀 다를 경우  
abstract method 를 이용하면 구현도 강제하고 다형성도 챙길 수 있다.  
따라서 abstract 는 이런 측면에서 의미가 있다.  
  
<br><br><br>
  
---

## 하지만, 기능이 전부가 아니다.  
  
기능이 전부가 아니다.  
  
자 처음으로 돌아가서,  
abstract 랑 interface 랑 똑같은식으로 쓸 수 있다.  
그러면 이렇게 생각해보자.  
  
### "애초부터 interface 가 지금의 abstract 처럼 동작하도록 했으면 됐잖아?"  
  
[참고한 글 - 인터페이스 vs 추상클래스 차이점 완벽 이해하기](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-vs-%EC%B6%94%EC%83%81%ED%81%B4%EB%9E%98%EC%8A%A4-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)  

먼저 기능 차이에 대해 추려보면    
![image](https://user-images.githubusercontent.com/101965836/212065399-6bf50844-433e-4470-a2a5-65351e6dd924.png)   
대충 이런 기능차이가 있다.  
  
기능 차이를 보면... 사실 거의 비슷해 보인다.  
저 기능의 차이가 무슨 if와 for의 차이만큼 크지도 않고  
float 과 double의 차이처럼 어떤 이유가 있지도 않다.  
  
그래서 저 차이점을 볼 때마다 드는 생각은...  
그냥 **애초부터 interface 가 abstract 처럼 동작하도록** 하면 됐잖아? 이다   
    
  
그러니까 저 차이점 표를 본다고 해서  
"아! abstract 랑 interface 는 다른거구나!" 라는 생각이 하나도 안들고  
"이거 왜 분리해둔거야?" 라는 생각이 든다는거다.  
  
### 다중상속 가능여부 빼고는... 딱히 interface의 의미가?  
interface가 다중상속이 가능하다는 점 빼고는   
abstract가 그냥 더 나아보일 수 있다.  
  
그런데 여기서.  
핵심이 다중상속이다.  
  
저 다중상속을 풀고 말고부터 시작해서  
어떻게 프로그램을 설계하고  
언제 abstract 와 interface 를 활용해야하는지 나뉘게 된다.  
  
  <br><br><br>  
  
## 상속(abstract) vs Interface  
책에서 대부분 상속과 인터페이스에 대해서는  
상속은 is a kind of    
인터페이스는 is able to   
라고 설명한다.  
  
예를 들어  
```
"Dog" is a kind of "Animal"
```
```
"Dog" is able to "Speak"
"Cat" is able to "Speak"
```
그러면 다시 생각해보자.  
a kind of 는 어떤 종류라는 뜻이고,  
able to 는 어떤 행동을 할 수 있다는 거다.  
  
여기서 조금 의문이 들 수 있지만  
상속과 인터페이스가 다중상속 가능 여부로 나뉜 이유에 대해 설명해보면 아래와 같다.  
```
종류는 하나에만 속할 수 있고  
행동은 여러 개 할 수 있다  
```
  
종류는 하나에만 속할 수 있다.  
동물이 포유류와 파충류에 동시에 속할 수 없는 것 처럼 말이다.  
  
반대로 행동은 여러 행동을 할 수 있다.  
걷기, 말하기, 표정짓기 등등  
하나의 동물이 여러 행동을 할 수도 있다.  
  
## 확장의 방향성이 다른거다  
상속이 큰 틀에서 공통된 부분을 묶는거고  
인터페이스는 어떤 특성을 첨가해준다.  
  
java의 기본 abstract 클래스와 interface에 어떤 것들이 있는지 생각해보자.  

---

### 상속 
AbstractList 는
큰 틀에서 List들은 모두들 AbstractList 를 상속해 구현한다.  

### 인터페이스
Comparable 은 객체간 비교할 수 있는 클래스에서 이를 구현해서 쓴다.  
Serializable 은 객체를 직렬화 할 수 있는 경우 이를 구현해서 쓴다.  

---
  
이를 통해서  
java에서 다중 상속을 막은 이유가  
다중 상속이 단순히 충돌 문제 때문에 금지하는게 아님을 알 수 있다.  
  
실제론 인터페이스를 통해서 C++ 같은 언어의 다중 상속을 java에서도 그대로 쓰고 있다  
대신 java는   
다중 상속이 가능한 인터페이스와  
다중 상속이 불가능한 추상클래스로 분리해놓은것이다.  
  
왜냐하면  
다중 상속 허용시 이점은 is able to 로 쓸 때 발휘되고  
다중 상속 금지시 이점은 is a kind of 로 쓸 때 발휘되기 때문이다.    
  
  
## 정리  
핵심은 다중 상속인 것 같다.  
  
고대의 선배 프로그래머들은 다중 상속에 문제가 있음을 깨달았다.  
하지만 그렇다고 무조건 단일 상속만으로는 "공통된 점"을 뽑아내는게 핵심인 객체지향에서 한계를 느꼈다.  
  
"공통 분류" 만으로는 뭔가 제대로 안됨을 깨닫고,  
이 문제를 어떻게 나눠서 어떤 경우에만 다중 상속을 쓰는게 좋을 지 생각해보기 시작했다.  
  
그래서 java에서 나온 결론이 interface 이다.    
매서드만 다중 상속이 가능하도록 해놓고, 각종 속성값은 다중 상속에서 얻지 못하도록 해둔 것이다.  
  
그러고 나서 또 보니까  
"상위 분류지만 이걸 객체로 만들일은 없는" 경우가 생겼다.  
  
예를 들면 Animal은  
공통적으로 Dog Cat Cow 같은 것들의 부모가 되는 종류기 때문에  
Animal 이라는 부모 클래스를 만들어두고 상속하는게 맞다.  
is a kind of 기 때문이다.  
  
근데 이런 경우는 어떨까?     
"내가 다루는 Animal은 전부 speak() 할 수 있어"  
그러면 Animal 에다가 speak() 메서드를 만들지만,  
speak() 클래스가 무조건 에러를 던지도록 놔두는 식으로  
오버라이딩을 강제해야할까?  
그냥 컴파일 타임에서 이를 강제해줄 방법이 없을까?  
  
이런 배경에서 나온게 abstract 이다.  
abstract 는 상속과 같이 맞물려서 사용되는 세트메뉴이고  
상속은 쓰지만, Animal 객체를 만들 일은 없고, Animal들이 공통적으로 어떤 메서드를 구현하도록 했으면 할 때  
abstract 를 쓰는거다.  
  
따라서  
Abstract Class 와 Interface의 차이가 뭐지? 라는 의문을 타고 올라가면   
상속과 interface 의 차이는 뭔가? 로 이어지고  
다중 상속 금지와 다중 상속 허용을 나눌 기준이 뭔가? 까지 이어진다.  
   
이런 질문으로 부터  
다중 상속을 무조건 막는거보단  
"행동의 다중 상속"은 허용하는게 좋다라는 결론에서  
interface 라는게 빠져나오게 됐다.  
  
그리고 큰 틀에서 "공통 분류" 에는 클래스 상속(단일)을 쓰도록 하고,  
"이 분류는 다 이 행동을 해야해" 라는 강제에서  
오버라이드를 안하면 에러를 던지는 방식은 컴파일 타임에서 문제가 안잡히니까  
abstract 라는 걸 써서 오버라이딩을 강제하도록 하자!  
라는 의도에서 abstract가 등장하게 된 것이다.  
  
  
### 그러므로, abstract 와 interface 는 존재 의미 자체가 다른거다  
그래서 abstract 로 interface 를 대체할 수 있잖아? 라는 의문은  
다중 상속을 왜 interface 에서는 풀었는가? 를 타고 들어가서  
interface 는 is able to 관계에 쓰기 위함이고 (행동; 메서드)    
abstract 는 상속 (is a kind of) 관계에 쓰기 위함이다.  
따라서 abstract 와 interface의 차이는 "존재 의의" 차이이다 로 결론지어진다.  
