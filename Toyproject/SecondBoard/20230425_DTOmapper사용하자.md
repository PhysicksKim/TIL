# DTO 어디서 만들어야 하는가?  

1. DTO 내부에 static factory method 를 만든다  

2. Mapper 를 사용한다  

두 방법 중 뭐가 더 좋냐고 하면  
여러 의견이 분분하고 장단점이 있다    
  
내가 이해한 바로는  

1. 팩토리 메서드 패턴 : 아주 간단하고 변할 일이 없지만, 조금 커스터마이징이 필요한 경우 
2. Mapper 라이브러리 : 유연하게 view에 맞춰야 하면서 간단히 값만 넣어 전달하는 경우  

이런 장단점이 있다  
  
<br><br>  

---

<br><br>  

# 가능하면, Mapper 를 쓰자    
[Don’t Create DTO Factories — Use Raven Instead!](https://medium.com/@ramindursingh/dont-create-dto-factories-use-raven-instead-662a80a9bfd2)  

위 글에서 말하는 바는 
Factory Method로 DTO 변환 로직을 만들면  
너무 Boiler-plate 코드가 많아진다는 단점을 지적한다.  
  
> Often, these factories are simply copying properties from one to the other,   
> perhaps with some logic. This results in lots of boiler-plate code.   

> DTO를 만드는 팩토리 메서드들은 대부분은 단순한 로직정도 더해서 값들을 복사할 뿐이다.  
> 이는 무수한 보일러 플레이트 코드를 만들어내는 결과를 낳는다.   
  
그러므로 위 글의 조언에 따라서  
최대한 Mapper를 활용하는 방향이 좋다고 생각한다.   
  
<br><br>  
  
# 내가 써볼 Mapper - MapStruct

[Mapstruct(DTO Mapper 라이브러리)](https://jiwondev.tistory.com/250)  
[Java ObjectMapper Benchmark](https://github.com/arey/java-object-mapper-benchmark)  
  
벤치마크상으로 우위이고   
여러 평이 좋고 사용하는 사람이 많은 것 같은 MapStruct 를 채택하기로 했다  
  
MapStruct 는 리플랙션을 이용하는 타 라이브러리와 달리 Anotation Processing 방식이다  
  
리플랙션은 런타임에서 객체의 캡슐화(private)를 뚫고 직접 접근하는 방식이다   
따라서 예외가 발생한다면 런타임에서 발생하기 때문에 미리 오류를 감지하기 힘들다.  
  
하지만 Anotation Processing을 사용하면 컴파일을 통해 동작하므로    
컴파일 과정에서 예외를 감지해서 미리 오류를 찾을 수 있다  
  
이외에도 MapStruct 의 장점이 주욱 나오는데  
지금은 학습 단계니까 일단 빨리 사용해보기나 하자  
  
