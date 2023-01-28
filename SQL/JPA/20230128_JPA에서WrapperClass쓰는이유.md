[Hibernate 레퍼런스](https://docs.jboss.org/hibernate/core/3.3/reference/en/html/persistent-classes.html)  
  
# 원래는 wrapper 클래스 사용을 지양해야한다  
![image](https://user-images.githubusercontent.com/101965836/215250172-49cb3555-0225-4dae-977f-ee61f23ab250.png)  
위 이미지는 이펙티브 자바의 목차인데  
이펙티브 자바에서도 박싱된 기본 타입(Wrapper Class) 보다는 기본 타입(Primitive Type)을 사용하는 것을 권장한다.  
  
그 이유를 간단히만 보면    
  
1. 성능상 문제  
2. 잘못된 비교 연산자 문제  

### 1. 성능상 문제  
  
![image](https://user-images.githubusercontent.com/101965836/215251050-f98ef575-5a12-46d0-ab03-c6b24bc12cee.png)  
[출처](https://velog.io/@power0080/Wrapper-%ED%81%B4%EB%9E%98%EC%8A%A4%EC%99%80-%EA%B8%B0%EB%B3%B8%ED%83%80%EC%9E%85)  
   
래퍼 클래스는 박싱 , 언박싱이 필요하다.  
기본형은 그냥 곧바로 더하는 연산이 가능하지만  
래퍼 클래스는 안에있는 값을 뽑아낸 다음 연산이 이뤄지므로  
값을 뽑아내는 과정이 계속 필요해서 성능상 기본 타입보다 느릴 수 밖에 없다.  
  
### 2. 잘못된 비교 연산자  
  
```java
Comparator<Integer> naturalOrder = 
  (i, j) -> (i < j) ? -1 : (i == j ? 0 : 1);
  
int result = naturalOrder.compare(new Integer(10), new Integer(10));

System.out.println(result); // 출력 : 1
```
  
같은 값이여서 0이 나와야 하는데  
비교 결과 1이 나왔다  
   
이는 new Integer() 로 직접 객체를 생성했기 때문이다.  
  
객체를 직접 생성하지 않고, primitive type에 값을 대입하는 방식대로 하면   
알아서 java가 오토박싱을 하기 때문에 문제가 해결된다.  
  
```java
Integer a = 10;
Integer b = 10;

int result = naturalOrder.compare(a, b);

System.out.println(result); // 출력 : 0
```
이렇게 new 로 객체 생성하지 않고 그냥 10을 집어넣으면  
java가 Immutable 한 Integer 객체로 오토박싱을 해주므로  
문제가 생기지 않는다.  
  
> 실제로도 Intellij 같은 IDE는 new Integer() 를 쓰면 취소선을 그어서 deprecated 되었음을 알려준다.    
  
  
### 잠재적 문제를 안고 있는 wrapper class를 꼭 쓸 필요가 없다  
개발자가 주의해서 사용한다 하더라도  
꼭 필요한 상황이 아니면, 그냥 더 나은 primitive type 을 사용하는 게 맞다.  
  
<br><br>  
  
# JPA(hibernate)에서는 @id 필드에 Wrapper Class 사용을 권장한다  
  
> We recommend that you declare consistently-named identifier properties on persistent classes and that you use a nullable (i.e., non-primitive) type.  
> 번역 : persistent classes에 일관되게 명명된 식별자를 선언하고, nullable 타입을 사용할 것을 권장합니다.   
  
보다시피 Identifier 속성에 nullable (즉, Wrapper Class) 사용을 권장하고 있다.  
  
왜일까?  
  
### 핵심은 Null 차이  
pk 필드가 null이면 아직 초기화되지 않았다 라는걸 명시적으로 알 수 있기 때문에  
id 필드는 nullable 하게 Long 같은 Wrapper Class 를 사용하는 것이다.  

