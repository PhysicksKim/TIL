# 문제 : Render가 두 번 일어나요 
강의를 따라하다가 분명히 똑같이 코드를 쳤는데 나는 Render가 두 번씩 일어나는 일이 생겼다  
  
왜인지 알아봤더니  
React 18 부터 Strict Mode를 사용하면  
Render가 2번 일어난다고 한다.  
  
# 그럼 Strict Mode 쓰지 말죠? - 아니. React는 Strict Mode 쓰는 것을 권장  
> We recommend wrapping your entire app in Strict Mode, especially for newly created apps.   
> 특히 새로 만들어진 앱에서는, 전체 앱이 Strict mode 태그로 감싸진 형태를 권장합니다.  

[React docs - \<StrictMode\>](https://react.dev/reference/react/StrictMode)  


<br>


# 2번 Render 되는 StrictMode를 써야한다    
  
### 근데 왜? 왜? 2번 render 하는건데?  
    
Strict Mode가 직역하면 "엄격한" 모드 라는건데  
**"왜 2번 랜더링 하는게 엄격하다는거지?"** 라는 의문이 생겼다  
  
> ### 참 아쉬운게 설명좀 해줬으면  
> "Render가 두 번 일어나요" 같은 식의 블로그 포스팅을 보면  
> 대부분은 아래와 같은 내용만 담겨있다  
> 1. Strict Mode 때문이니까 Strict Mode를 해제해라  
> 2. Strict Mode 때문이니고, Render가 2번 일어나서 그런거니 코드를 다시 작성해라  
>   
> 근데 왜?  
> 왜 Render 2번을 가정하고 코드를 작성하면  
> 더 좋은 코드가 되는지 설명을 찾기 힘들었다  
>   
> 물론 이 내용이 마치   
> 이제 객체 만드는법을 배운 초보에게  
> 어떤게 좋은 객체지향 코드인지 알려달라는 말 같지만    
> 그래도 최소 키워드나 본인의 추측이라도 남겨놨으면 내가 좀 덜 해맸을텐데  
> 아쉽다.  

<br><br>  

# 이유 : 너가 순수 함수로 만들었다면, 2번 랜더링해도 문제 없겠지?  
> 순수 함수가 뭔지는 안다고 가정한다.  
  
### 1. React는 Component를 순수함수로 만드는 것을 권장한다 
[React Docs - Keeping Components Pure](https://react.dev/learn/keeping-components-pure)   
    
Component가 순수함수여야 하는 이유는 당연히  
순수함수의 장점과 같다  
"입력이 동일하면 출력도 동일하다"  
  
따라서 모든 Component가 순수함수면  
더 좋은 코드 구조를 가질 수 있고  
디버깅도 쉬워지며  
뜻하지 않은 동작이 일어날 위험도 적어진다  
  
따라서 React는 애초에 공식 튜토리얼부터    
Component를 순수함수로 만들어라고 따로 문서까지 있을 정도로 강조하고 있다.  
  
### 2. 2번 랜더링은 비순수함수를 잡기 위함  
순수함수라면 
입력이 몇 번이고 들어와도  
동일한 출력을 보여줘야 한다.  
  
근데 만약 비순수함수라면?  
2번의 입력이 들어왔다면  
예를 들어 콜백 함수가 두 개씩 들어가는 문제들이 발생할거다  
  
따라서 React는 이런 문제들을 어떻게 조기에 잡고  
이외의 흔한 bug들을 개발 단계에서 발견할 수 있을지 고민했다  
  
이러한 고찰 끝에 React의 결론은   
Render를 2번 하면 순수 함수인지를 효과적으로 판별할 수 있다고 결론내린듯 하다.    
그래서 Stirct 모드에서 랜더링을 2번하도록 해서  
Component를 순수 함수로 유지하도록 유도한 것 같다.  
  
<br><br>  

# 요약  
React.StrictMode 에서는 Render가 2번 일어난다 (LifeCycle이 2번)    
2번 랜더링 하는 이유는 React가 component들이 순수함수인지 검증하려는 의도이다.   
만약 component들이 순수함수라면, 랜더링을 2번해도 문제가 발생하지 않을거다.  
하지만 2번 랜더링했을 때 문제가 발생한다면,   
해당 component는 순수함수가 아니기 때문에 문제가 발생한거다  
그러니 StrictMode의 제약은 더 좋은 코드를 만들기 위한 제약이니까  
순수함수로 다시 작성하자.  
  


