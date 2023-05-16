# POJO가 뭔지 제대로 설명좀  
처음 Spring을 배울 때 대뜸 아래와 같은 그림이 등장했다  
  
- Spring 기본 철학으로 유명한 "Spring 삼각형"
![image](https://github.com/PhysicksKim/TIL/assets/101965836/882eab04-98d3-415a-9e60-0d0197f1f92c)  
  
바로 "Spring 삼각형" 이다.   
처음 웹개발을 배울 때는 철학이 어떻고 하는 말이 하나도 이해가 안됐다  
    
- 삼각형이 뜻하는 바  
> Spring은 POJO를 기본 철학으로 하며  
> IoC/DI , AOP , PSA(Portable Service Abstraction)  
> 3가지를 통해서 POJO를 실현한다  
  
일단 내가 Spring을 처음 배울 때  
저 3가지를 이렇게 이해하고 대충 넘어갔다    
> IoC/DI는 스프링이 @Autowired 보고 구현체 집어넣어주는거    
> AOP는 Annotation을 보고 스프링이 메서드 앞 뒤에서 어떤 작업을 처리해주는 거    
> PSA는 이거 쓰다가 저거 써도 되는 거, MySQL쓰다가 PostgreSQL 써도 된다는 거   
  
### 근데 POJO가 저거랑 무슨 상관이야?  
POJO가 그래서 뭐고 왜 하는건데?  
기본 자바 객체 쓴다는건 알겠는데 그게 뭐 어때서?  
  
라고 생각했다  
  
그런데 돌고돌아 지금에 이르러서  
POJO에 대해 갑자기 번뜩 알게된 것 같다.  
  
<br><br>  

# 쉽게 말해 POJO는 호환성이다  
  
기존 코드는 그대로 두면서  
새로운 것을 추가하기 쉽도록 하는 것.  
  
아키텍처나 디자인 패턴에서 항상 등장하는 이야기다  
POJO도 마찬가지로 위와 같은 관점에서 나온 것이다  
  
그러면 POJO가 뭔지 알기 전에,  
문제가 발생하는 시나리오부터 보자  
  
<br><br>  
  
# Library 마다 다른 객체를 쓴다면?  
만약에 **2차원 행렬 계산**을 위한 라이브러리가 있다고 해보자  
  
<br>
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/7f49f175-d7fa-4161-beec-5432bb2f3be6)  
그런데 이 라이브러리는 CoolMatrix 라는 타입을 사용하는데   
개발자가 **"직접 CoolMatrix 라는 타입을 사용해야 한다"** 라면 무슨 문제가 생길까?  
  
<br>  
  
### CoolMatrix에 의존  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/2ace28d1-01bc-4619-a46a-82087fe8b5e8)  
의존한다는 말은  
코드에 직접 CoolMatrix 가 들어간다는 말이다  
  
```java
public void receiveData(CoolMatrix data) {
  ...
}
```
이러면 Parameter에 CoolMatrix가 들어있으니  
라이브러리에 의존적인 코드가 된다  
  
### 다른 라이브러리 변경/추가 어려움  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/c0db9959-b8e0-4515-bb80-b7b60e584f6f)  
  
위와 같이 다른 라이브러리로 교체한다 해보자   
그런데 기존 코드들이 다 CoolMatrix에 의존적이기 때문에  
새로운 라이브러리로 변경이 쉽지 않다  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/fb4dafd5-14c0-486c-bcd7-93e6896a62c9)  
이렇게 일일이 CoolMatrix를 의존하는 새로운 코드를 만들어 주고   
기존 코드와 이어줘야 하기 때문이다  
  
### 추가적인 문제 
예를 들어 CoolMatrix 라이브러리가 유행하기 시작했고  
3년동안 CoolMatrix를 사용하는 수 많은 다른 라이브러리들이 탄생했다고 하자  
  
그런데 AwesomeMatrix 라는 라이브러리가 새로 등장해서  
기존 CoolMatrix에서 변화하려 하는 데  
이미 수많은 Library들이 CoolMatrix에 의존하고 있으므로 
CoolMatrix를 또 AwesomeMatrix로 변환해주는 라이브러리가 필요하다  
  
<br><br><br>  
  
# 그래서 POJO 등장  
POJO는 뭐냐 하면   
Plain Old Java Object   
int, double, boolean, String, List,     
이런 기본 Java Object에 의존하자는 뜻이다.  
  
물론 라이브러리 내부에서는 커스텀 객체를 사용할 수 있겠지만  
개발자 코드에는 특정 라이브러리에만 있는 객체가 등장하지 않는다는 것  
오로지 라이브러리는 Input Output으로 Java 객체만 사용한다  
  
<br><br><br>  
  
# 그런데 Spring 전용 객체들이 있잖아요?  
  
정작 Spring 코딩하다 보면 JdbcTemplate 같이  
기본 Java 객체가 아닌 것들이 등장한다  
  
그러면 Java 객체가 아닌 것들이 등장하니 문제되지 않을까? 싶은데  
이는 어차피 Spring Framework를 쓰는 순간부터  
처음부터 끝까지 Spring에 의존해야 할 수 밖에 없으므로  
Spring에서 제시하는 표준 객체는 문제가 없다.  
  
오히려 Spring이 편리한 표준 인터페이스를 제시하고  
이에 맞는 구현체들을 Spring이 잘 연결시켜주기 때문에  
POJO를 실현할 수 있게 된다.  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/882eab04-98d3-415a-9e60-0d0197f1f92c)    
   
그리고 "잘 연결시켜주는 방법"이 바로 앞서 본 Spring 삼각형의 3가지 도구  
IoC/DI , AOP , RSA 인 것이다    
  

  

