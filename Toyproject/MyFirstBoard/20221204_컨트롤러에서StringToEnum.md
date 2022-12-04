# Enum 사용이 필요해졌다  

게시판 검색에서 검색조건으로 제목, 내용, 작성자 , 제목+내용 이렇게 4가지 선택하는 부분이 있다.  
이걸 그냥 String으로 받아서 처리해도 되겠지만, 당연히 String을 쌩으로 이용하면 아래와 같은 2가지 문제가 생긴다.  
  
1. 오타 발생시 런타임에서 문제 생김
2. 컴파일러 자동완성이 불가능하므로 귀찮아짐  
  
물론 위의 문제를   
따로 SearchTypes 라고 상수 클래스를 빼는식으로 해서  
```java
public static final String TITLE = "title";
public static final String WRITER = "writer";
...
```
이렇게 해놓고 쓸 수 있기는 하다.  
하지만 이렇게 String 테이블 빼서 쓰면  
컴파일러나 프레임워크의 지원을 제대로 받을 수 없다는 단점이 있고,  
경우에 따라서는 Enum으로 더 쉽게 문제를 해결할 수 있기도 하다.  
  
<br><br><br>  
  
# Java Enum 장점  
  
[참고 Java Enum 활용기](https://techblog.woowahan.com/2527/)  
위 글에서 말하는  
Java Enum 장점을 인용하면  
  
> 개발을 진행할때 Enum을 통해 얻는 기본적인 장점들은 아래와 같습니다.  
>   
> 문자열과 비교해, IDE의 적극적인 지원을 받을 수 있습니다.  
> 자동완성, 오타검증, 텍스트 리팩토링 등등  
> 허용 가능한 값들을 제한할 수 있습니다.  
> 리팩토링시 변경 범위가 최소화 됩니다.  
> 내용의 추가가 필요하더라도, Enum 코드외에 수정할 필요가 없습니다.  
>   
> ...  
>    
> 하지만 Java의 Enum은 이보다 더 많은 장점을 갖고 있습니다.   
> C/C++의 경우 Enum이 결국 int값이지만, Java의 Enum은 완전한 기능을 갖춘 클래스이기 때문입니다.  
  
위 링크 글의 내용을 덧붙여 내가 정리해보면      
Java Enum은 메서드도 담을 수 있고, 다양한 값을 같은 Enum value로 바꿀 수 있는 등 유용한 점이 많다고 한다.  
  
이렇듯 선택지가 주어지고 이중에 골라라는 식의 값을 사용자로 받아올때는 enum을 사용하는게 좋은 코드를 만든다고 생각이 들어서  
enum을 써보기로 했다. 
  
> 언어의 철학을 따르는게 대채로 옳다.  
> 해당 언어가 "이럴때는 이런걸 써라" 라고 만들어 뒀으면, 그걸 그대로 따르는게 '그 언어'스럽게 코딩하는 길이기 떄문이다  
  
<br><br><br>  

# Form Select 태그 -> Spring Controller 받아오기  
  
[Thymeleaf) form형식에서 Enum type 값 받기](https://velog.io/@nestour95/Thymeleaf-form%ED%98%95%EC%8B%9D%EC%97%90%EC%84%9C-Enum-type-%EA%B0%92-%EB%B0%9B%EA%B8%B0)   
위 글을 참고해서 코드를 작성했다    
 
<br><br>  
 
# 문제 발생    
View 에서는 camelCase 로 네이밍 하고 (ex. title, titleOrContent)  
Enum 은 java의 전통적인 네이밍 규칙에 따라 전부 대문자로 했다 (ex. TITLE, TITLEORCONTENT)  

### View - camelCase   
<img width="473" alt="image" src="https://user-images.githubusercontent.com/101965836/205480183-a2297e75-62f7-4021-996c-b39db86d2ee8.png">  
  
### Enum - upperCase  
<img width="249" alt="image" src="https://user-images.githubusercontent.com/101965836/205480872-180ed142-49c7-4949-a9ca-4b558df7d7c2.png">  
  
title과 TITLE 이 대소문자가 달라서 아래와 같이 mistMatch 에러가 발생했다  
<img width="1312" alt="image" src="https://user-images.githubusercontent.com/101965836/205480318-ddc2e93c-be7a-451f-8384-3d48bd19a05d.png">  
  
<br><br><br>  
  
# 해결법1 - upperCase로 통일  
현재 프로젝트에서는 복잡해질 필요가 없다고 생각해서  
select 태그쪽 value들을 전부 UPPERCASE 로 바꾸도록 했다  
  
<img width="447" alt="image" src="https://user-images.githubusercontent.com/101965836/205480950-1d97a09c-bb7d-4b36-8e83-cb945b5b3784.png">  
이렇게 바꿔주니  
<img width="1223" alt="image" src="https://user-images.githubusercontent.com/101965836/205480988-bf7a82f6-9c6e-44a5-838a-7ae4c9684553.png">  
이렇게 log를 통해서 잘 바인딩 된 것을 확인할 수 있었다  
  
<br><br><br>  
  
# 해결법2 - customConverterFactory 
프로젝트에 적용은 안했지만 또 다른 해결법도 있다  
대소문자 네이밍 규칙때문에 발생한거니, 대소문자를 무시하고 매칭하도록 
StringToEnum Converter 동작을 커스텀해줄 수 있다  
    
[참고 Enum and @RequestParam in Spring](https://kapentaz.github.io/java/spring/Enum-and-@RequestParam-in-Spring/#)  
     
이 방식이 유용할 상황을 가정해보자.  
여러 기업들의 이름을 다루는 Enum이 있는데  
알다시피 facebook 이 meta로 사명을 바꾼 상황이다.     
근데 사람들은 아직도 facebook 이라는 명칭을 선호하고, 개발자들도 사람에 따라 meta , facebook 을 섞어 쓰곤 한다.    
그래서 "meta" "facebook" 을 둘 다 enum의 FACEBOOK에 대응하도록 해주고 싶게됐다.  
  
이 경우 customConverter가 매우 유용하게 쓰일 수 있을 것 같다.  
  
