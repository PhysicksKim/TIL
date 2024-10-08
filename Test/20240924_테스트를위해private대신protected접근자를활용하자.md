> gpt 로 다듬은 글 

# private 접근자는 테스트 코드에서 접근할 수 없다
자바에서 private 접근자로 선언된 메서드나 필드는 해당 클래스 내부에서만 접근할 수 있다. 그래서 테스트 코드에서 이 private 멤버들에 직접 접근하려고 하면 컴파일 오류가 발생한다.

이럴 때마다 나는 "테스트를 위해 이 메서드를 호출하고 싶은데, 방법이 없을까?"라는 고민을 하게 된다. 물론 리플렉션을 사용해서 억지로 접근할 수 있지만, 이는 권장되지 않는 방법이다.

<br><br><br>

# protected 접근자를 사용하면 접근 가능하다
하지만 protected 접근자로 선언하면 이야기가 달라진다. 테스트 코드에서 protected 멤버에 접근할 수 있게 된다.

## 어떤 원리로 접근 가능한가?
처음에는 나도 이게 어떻게 가능한지 의문이었다. 알고 보니 자바의 접근 제어자는 패키지와 상속과 깊은 관련이 있었다.

protected 접근자는 같은 패키지 내의 클래스나 상속받은 하위 클래스에서 접근이 가능하다. 테스트 코드는 일반적으로 소스 코드와 동일한 패키지 구조를 유지한다. 그래서 테스트 클래스는 동일한 패키지에 속하게 되고, protected 멤버에 접근할 수 있게 되는 것이다.

### 예를 들어,

소스 코드 클래스: com.example.myapp.service.MyService <br> 
테스트 코드 클래스: com.example.myapp.service.MyServiceTest <br> 
이렇게 패키지 구조를 유지하면, 테스트 코드에서 protected 멤버에 자연스럽게 접근할 수 있다.

<br><br><br>

# 실제로도 protected 접근자를 이렇게 활용하는가?
생각해보면, 테스트를 위해 private 메서드를 protected로 바꾸는 것이 캡슐화를 해치는 것 아닌가 하는 걱정이 들었다.

하지만 생각해보면 자식으로 상속하여 구현하는 경우에는 해당 개발자가 도메인에 대한 충분한 이해가 있는 뒤에 이뤄지므로, 테스트를 위한 private 메서드의 접근자를 protected 로 변경하는 것은 크게 문제되지 않을 것 같다.  
중요한 비즈니스 로직이나 외부에 노출하고 싶지 않은 메서드는 private으로 유지하고, 테스트가 필요한 경우에 한해 protected로 선언하여 테스트 코드에서 접근할 수 있도록 하는 게 옳은 것 같다.  
이는 캡슐화를 유지하면서도 테스트 가능성을 높이는 방법이다. 또한, 패키지 구조를 잘 활용하면 접근 범위를 과도하게 넓히지 않고도(public) 필요한 테스트를 수행할 수 있다.  
  
<br><br><br>

# protected 접근자에 대한 자바 언어적 고찰
자바 초기 언어 설계에서는 아마 "패키지 단위의 구성" 을 고려했던 것 같다.  
사실 패키지 단위의 구성은 크게 의식하지 않고 설계하기 마련인데, 테스트 코드에서 protected 를 활용해서 private 대신해서 접근 범위를 확보한다는 점을 보면 좀 흥미롭다.  
언어 철학 차원에서 "패키지 단위 접근" 이라는 것을 고려한 덕분에 테스트 코드에서 패키지 단위에서 접근 가능한 접근을 활용하여 메서드를 은닉시킬 수 있었다는 것이다.  
protected 를 처음 보면 자식에 쓰인다는 점은 알겠는데 실질적으로 내가 코드를 짤 때 언제 protected 를 쓸지 참 어려웠었는데,  
그나마 조금 쉽게 이해 가능한 protected 활용처에 대해서 하나 이야기 할 수 있게 된 셈이다.     
  
### 접근 제어자를 통해 계층적인 접근 구조를 만든거라 생각된다 
  
public: 어디서나 접근 가능 <br>
protected: 같은 패키지 또는 상속받은 하위 클래스에서 접근 가능 <br>
(default): 같은 패키지 내에서만 접근 가능 <br>
private: 해당 클래스 내에서만 접근 가능 <br>
  
특히 protected 접근자는 패키지 내부의 협업과 상속을 통한 확장성을 모두 지원하기 위해 설계되었다.   
자바는 패키지를 단위로 모듈화를 생각했고, 패키지 내부에서 클래스들이 협력할 수 있도록 protected와 패키지 프라이빗(default) 접근자를 제공했다.  
   
또한 상속 관계에서도 접근을 허용하여 재사용성과 확장성을 높이고자 했다. 이는 자바 언어의 설계 철학이 반영된 결과인 것 같다.   
  
<br><br> 

---

<br>
  
생각해보면, 자바의 접근 제어자는 코드의 보안성과 안정성을 높이기 위한 계층 구조를 이루고 있다. <br> 
이런 점에서 테스트 코드에서 protected 접근자를 활용하는 것은 자바 언어의 특성을 잘 활용한 사례라고 할 수 있다.  
  
캡슐화를 해치지 않으면서도 테스트 가능성을 높일 수 있고, 이는 좋은 개발 습관이라고 생각한다.  
