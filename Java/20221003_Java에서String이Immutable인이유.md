### 참고  
[Youtube - "Java Strings are Immutable - Here's What That Actually Means"](https://www.youtube.com/watch?v=Bj9Mx_Lx3q4)  
[String Constant Pool이란? | Java String Pool](https://starkying.tistory.com/entry/what-is-java-string-pool)  
  
[[Java] String이 불변 객체인 이유는 무엇일까?](https://devlog-wjdrbs96.tistory.com/247)
  
---
  
  <br><br><br>
  
# String 이 Immutable 하다는 것은?  

```java
String str = "Hello";
str = "World";  
```
  
위와 같은 코드를 예시로 들어보자.     
str 이 "Hello" 객체를 가리키고 있다가,  
다시 = "World" 라고 바꿨다.  
  
이때 실제로는 "Hello" 객체의 값이 바뀐게 아니다.  
  
실제 동작은    
"World" 라는 새로운 객체를 만들고    
str이 "World" 객체를 가리키도록 한다.  
  
<br><br><br>  
  
그렇다면  
**Java의 String은 왜 이런식으로 동작할까?**  
  
<br><br><br>

# 결론부터 : String Pool로 메모리 절약이 가능해진다  
  
만약 아래와 같은 코드가 있다고 해보자  
```java
String name = "Kim";
String anotherName = "Kim";
String aThirdName = "Kim";
```
이러면 3개의 "Kim" 객체가 만들어진다고 착각할 수 있다.  
하지만 실제로는 1개의 "Kim" 객체만 있고, 3개의 참조 변수가 생길 뿐이다.  
즉, name , anotherName , aThirdName 모두 다 "Kim" 객체를 가리키고 있는 것이다.  
따라서 3개의 "Kim"을 만들 때 보다, 1개의 "Kim"을 3개의 참조형 변수가 가리키게 되어서  
메모리 절약에서 탁월한 효과를 보이게 된다.  
  
<br><br><br>  
  
# 어떻게 동작하는가?  
   
### String Pool
   
![image](https://user-images.githubusercontent.com/101965836/193465524-c1bc3f51-afb5-4b02-96e1-b44ef354b251.png)  
  
String을 처음 생성하면,  
제일 먼저 Java가 String Pool 이라는 곳을 뒤져본다.  
String name = "Kim"; 으로  처음으로 "Kim"에 대해 뒤져보고, 없으면 새로 만들어서 String Pool에다가 집어넣어놓고 참조하도록 한다.  
  
그 다음 String anotherName = "Kim"에서도 역시  
처음에 String Pool에서 "Kim"이 있나 찾아보고  
앞서 만들어둔 "Kim"이 String Pool에 있으니  
말했다시피 메모리 절약을 위해서 기존에 만들어져있던 "Kim"을 그냥 참조하도록 한다.  
   
<br><br>  

## new 연산자 ; String Pool 밖에 생성  
위 그림에서 보다시피  
```java
String s3 = new String("Cat");
```
이렇게 new 연산자를 사용하면 String Pool 밖에 생성된다.  
  
  <br>  
  
### String Pool 밖에 생성됐는지 확인하는 법 
```java
String s1 = "Cat";
String s2 = "Cat";
String s3 = new String("Cat");
```
  
![image](https://user-images.githubusercontent.com/101965836/193466177-048886ff-5f39-4b43-9754-b9ec257bb39c.png)    
  
s1과 s2는 String Pool에 생성되어있는 "Cat"을 가리키기 때문에  
**s1 == s2** 는 같은객체라서 **true** 된다.  
하지만 **s3은 new로 생성**했으므로  
**s1 == s3** 하면 **false**가 된다.  
  
<br><br><br>  
  
# 이외의 Immutable 장점  
## 1. Thread-Safe   
불변(Immutable)이니까, 같은 객체를 여러 곳에서 참조하더라도,  
어차피 불변이라서 객체의 값이 변할 걱정이 없다.  
따라서 Thread-Safe하게 String 객체를 쓸 수 있다.  
  
<br>  

## 2. 해시코드 캐싱(Hashcode Caching)
```java
public final class String {

    int hash; 
    
    ...
    
    public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
    }
}
```
위 코드는 String 클래스의 hashCode() 메서드 코드이다.  
  
결론부터 말하면, 객체가 Immutable이니까 hash 값을 한번 계산하고 계속 쓸 수 있다.   
값이 불변이니까, 객체의 값에 따라 Hash값이 바뀔 걱정을 안해도 된다.  
따라서 hash 값을 위 코드처럼 Caching 해두고 쓸 수 있다.  
  
  <br>  
  
코드도 위처럼 간단하다.  
String 객체에는 hash 라는 필드 변수를 갖고있다.  
말 그대로 hash 값을 저장해두는 필드 변수다  
  
hash 값의 초기화는 객체가 생성될 때가 아니라  
hashCode() 메서드가 처음 실행될 때 이뤄진다.  
  
따라서 위 코드에서 if문으로  
**(hash값이 초기화 되지 않았고 && String이 비어있지 않으면)**   
으로 걸러서  

만약 hash 값이 생성되지 않았으면, if{ } 안의 로직에 따라 값을 생성해서 hash에 넣어주고 return 한다.  
만약 hash 값이 이미 생성됐으면 곧바로 return hash 해준다.  
  
  <br>  

따라서 이렇게 **HashCode를 Caching해두는 덕에**  
**HashMap 같은 곳에서 String을 쓸 때 성능상 이점**이 있다.  

<br>
  
## 3. 보안 
예를 들어 은행 어플리케이션이라고 해보자.  
  
은행 어플리케이션은 돈이 오고가는 문제가 달렸기 때문에  
계좌 이체가 정확하게 되는 것이 매우 중요하다.  
   
근데 만약 메소드가 막 진행되다가  
String이 Mutable해서 값이 중간에 바뀐다면?  
다른 계좌번호로 돈이 들어가거나  
돈이 10,000원에서 100,000원으로 복사가 되는 대참사가 일어날 수 있다.  
  
### 대참사 예시  
```java

public class Disaster {

  // 계좌에 돈을 넣어주는 메소드
  void criticalMethod(String account) {
    // 유효성 검사
    if(!isTargetToAddMoney(account)) throw new AccountError();
    
    // 시간이 걸리는 작업
    doTask(account);
    
    // 여기서 account 값이 바뀐다면?  
    
    // 계좌에 돈을 올려줌
    plusMoney(account); 
  }
}
```

이렇게 주석에 따라 중간에 String account 값이 다른 메소드로 인해서 바뀐다면  
엉뚱한 account에다가 돈을 올려주게 될 수 있다.  
  
따라서 이런 참사를 막기 위해 String 이 Immutable 하면 훨씬 안전해진다.  
  
