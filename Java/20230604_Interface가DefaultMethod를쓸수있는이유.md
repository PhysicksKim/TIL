# 갑자기 왜?  
Abstract Class를 보다보니, Java가 왜 java 8 부터 Interface에서도 구현된 메서드를 가질 수 있도록 바뀌었는지 의문이 들었다.  

### Interface VS Abstract Class 차이 모른다면  
[먼저 읽어보세요 - 20230112_abstract와interface차이.md](https://github.com/PhysicksKim/TIL/blob/0d8a9ec3171a06d8e2ee6382b4a80010ef751e3b/Java/20230112_abstract%EC%99%80interface%EC%B0%A8%EC%9D%B4.md)  
  
<br>  
  
### Abstract Class를 보던 중...  
StringBuilder의 소스코드를 봤는데  
AbstractStringBuilder 가 있었다.  
  
AbstractStringBuilder를 상속한 클래스는  
StringBuilder 와 StringBuffer 두 가지 이다.   
둘의 동작이 비슷하기에 AbstractStringBuilder로 묶은 것.   
   
그리고 String 에서  
```java 
if (cs instanceOf AbstractStringBuilder) {
  ...
}
``` 
이런 식으로 다형성을 활용해 코드를 구성하고 있는 것을 확인했다.  
  
그런데 왜 Abstract Class를 썼을까?  
혹시, StringBuildable 이라는 Interface를 따로 빼면 안되는걸까?  
  
<br>
  
# 내 생각   
- 분류의 차이. 상태를 가질 수 있는가의 차이.  
  
Class 상속이 의미하는 바와  
Interface 구현이 의미하는 바가 다르다.  
  
흔히들 is-a , has-a 관계라고 이야기 한다.  
그치만 좀 더 명료하게는 is a kind of, is able to do 관계라고 나는 이해하고 있다.   
(책에서 봤다 - 객체지향 프로그래밍\[김동현\])   
  
<br>  
  
### 예시. AbstractStringBuilder VS StringBuildable  
- AbstractStringBuilder : 문자열을 쌓아올리는 종류의 클래스들    
- StringBuildable : 문자열을 쌓아올릴 수 있음  
  
이렇게만 보면 또 애매하다. 둘 다 그럴싸한데?  
    
여기서 또 하나 추가해야하는 개념이 바로 상태(state)이다    
  
<br>  
  
### 상태 (stateful) / 무상태 (stateless) 
: Method가 field 값에 따라서 결과가 다르다. 즉, 상태에 따라 다르다.      
  
중요한 차이점     
Abstract Class는 Field를 가질 수 있고   
Interface는 Field를 가질 수 없다.   
   
이게 무슨 말이냐면   
Abstract Class에 있는 method들은   
Parameter 외에도 다른 Field의 상태에 따라서 method가 돌아간다는 말이다.  
    
<br>  
    
### Field가 왜 상태?  
예를들어 AbstractStringBuilder 를 보자  
  
```java
abstract class AbstractStringBuilder implements Appendable, CharSequence {
  /**
   * The value is used for character storage.
   */
  byte[] value;

  /**
   * The id of the encoding used to encode the bytes in {@code value}.
   */
  byte coder;

  /**
   * The count is the number of characters used.
   */
  int count;

  private static final byte[] EMPTYVALUE = new byte[0]; // 이건 상수니까 논외. 상수는 상태를 나타내지 않음.  

  ...
```
보면 'character를 저장할 배열', '인코딩 방식', '배열 카운트 변수' 같은 필드가 있다.   
  
AbstractStringBuilder class의 method는   
field 값에 따라서 method 결과가 달라진다.  
  
<br>  
  
### 그냥 Class가 아니라 Abstract인 이유는?  
  
AbstractStringBuilder의 abstract method로는 toString() 이 있다.    
  
```java
@Override
public abstract String toString();
```
  
StringBuilder와 StringBuffer가 
toString() 메서드를 구현하는 방식이 서로 다르다는 뜻이다.    
  
이렇게 Abstract로 @Override 를 강조하는 한편  
둘 다 공통으로 toString()을 Override 할 때  
AbstractStringBuilder의 field를 사용한다.  
  
### value와 count가 AbstractStringBuilder의 field다  
  
- StringBuilder의 toString() 
```java
@Override
@HotSpotIntrinsicCandidate
public String toString() {
    // Create a copy, don't share the array
    return isLatin1() ? StringLatin1.newString(value, 0, count)
                      : StringUTF16.newString(value, 0, count);
}
```
  
- StringBuffer의 toString()  
```java
@Override
@HotSpotIntrinsicCandidate
public synchronized String toString() {
    if (toStringCache == null) {
        return toStringCache =
                isLatin1() ? StringLatin1.newString(value, 0, count)
                           : StringUTF16.newString(value, 0, count);
    }
    return new String(toStringCache);
}  
```   
  
이유는 모르더라도 Abstract에 관해 2가지를 옅볼 수 있다  
1. Abstract Method가 Override를 강제했다. (구조적인 부분을 강제할 수 있다는 점에 의미가 있다)  
2. Abstract Method에서 Abstract Field를 쓸 수 있다.   
  
여기서 2가 Abstract Class의 차별점이다.  
Abstract Class에서 Concrete Method(구현끝난 메서드)에서 Field를 사용할 수 있고,  
Extends 한 Class에서는 해당 Field를 쓸 수 있다.  
  
즉, Abstract Class에서 정의한 "공통으로 가져야 할 상태(field)"를  
하위에 extends한 클래스들도 공통으로 필요로 할 때  
Abstract Class로 공통으로 묶어주는 것에 의미가 생긴다.  
  
<br><br>  
  
# 그러면, 공통 상태를 가지면 무조건 Abstract로 빼내야 할까요?  
  
이건 결국 선택의 문제다.  

> ### Abstract로 빼낼 때 의미가 있는가?  
  
이건 그때그때 판단해야한다.  
다만 Interface로 뺄 수 있는 method와  
Abstract Class로 뺄 수 있는 method의 차이는  
계속해서 말한 것 처럼  

> field를 필요로 하는가?  

이라는 점에서 차이가 있다.  
  
Interface : 넘어온 Interface에 따라 달라짐.  
Abstract Class : Method들이 공통으로 필요로 하는 field가 있고, 이를 하위 클래스에서도 필요로 하는 경우.  
  
<br><br>  

# 그러면, field가 필요하면 Abstract Class로 빼야할까요?  
  
이것도 설계의 문제다.  

이걸 Interface로 빼낼 때 구조가 더 깔끔해질까?  
아니면 Abstract Class로 빼낼 때 구조가 더 깔끔해질까?  
  
이는 다시 원론으로 돌아가서  
Class 상속은 is a kind of 일 때  
Interface 구현은 is able to do 일 때  
사용한다는 기본 원칙을 따져봐야 한다.  
  
### 상속과 구현에 관한 예시는 객체지향 책으로 공부  
너무 할 말이 많고 논쟁도 잦은 부분이 
class 상속과 interface 구현 뭐가 맞는가 문제이다.  
아직 나도 초보니까 실제 코드에서 자주 헤맨다.  
  
다만 기본 원칙이 저런걸 이제 알았으니  
실제로 이런 문제에 마주쳤을 때  
이 기본원칙들을 떠올리며 구심점을 잡고 내공을 쌓아갈 수 있게 됐다.      
  
