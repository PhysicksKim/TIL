# 표준 입력에 사용되는 BufferedReader 와 Scanner  
  
대부분 알고리즘 문제 사이트에서는   
Method Argument를 통해 Input을 얻고, Return 값으로 Output을 준다.    
ex) <code>public int solution(int input)</code>   
   
반면 특이하게도  
백준 알고리즘 사이트에서는 표준 입출력을 사용한다.  
그리고 Java로 알고리즘 문제에서 표준 입력을 받을 때 사용하는 대표적인 두 가지가    
<code>java.util.Scanner</code> / <code>java.io.BufferedReader</code>   
이렇게 2가지 이다.  

<br><br>

# 속도 차이  
두 방식 차이의 핵심은 속도이다.  
속도 차이 비교는 아래 링크에서 얻어왔다.    
[입력 속도 비교](https://www.acmicpc.net/blog/search/%EC%9E%85%EB%A0%A5+%EC%86%8D%EB%8F%84)  
  
위 자료에서  
  
|순위|언어|입력 방법|평균 (초)|
|---|---|---|---|
|6|Java|BufferedReader, Integer.parseInt|0.6585|
|17|Java|Scanner|4.8448|  
  
이렇게 확연한 속도 차이를 볼 수 있다.  
  
<br><br>  

# Scanner vs BufferedReader 
결과적으로는 속도와 유틸성의 차이이지만.  
구체적으로 어떤 이유인지 궁금했다.  

막연하게 "두 표준 입력이 무슨 차이일까?"를 추측만 하고 있었다.  
아마도 BufferedReader는 Buffer를 써서 더 빠르고, Scanner는 util 기능이 더 많으니 좀 더 느린 것 아닐까?  

그런데 좀 구체적으로 명확히 알고 싶어져서 찾아봤다.  
    
<br><br>

# GPT의 설명 

GPT 는 Buffer 유무와 편의성을 중시한 Scanner의 메서드 들로 인해 속도 차이가 발생한다고 설명한다.  
  
여기서 앞의 Buffer 유무 이유가 핵심이라고 생각한다.  
  
<br><br>  
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/06da3f00-904c-40b0-96c4-e1cae41b183a)  

GPT가 그려준 Diagram이다.   
Buffer 개념과 속도 향상은 당연히 알고 있었는데    
구체적으로 어디를 대상으로 Buffer를 적용시켜서 속도 차이가 나는거야? 라는 의문이 들었다.    
   
<br>  
   
Buffer 속도 향상은   
조각난 데이터를 읽을때나  
두 장치간 속도 차이가 있는 경우에  
눈에 띄게 속도 향상을 얻을 수 있다고 알고있다.  

따라서 조금 생각을 바꾸면 명료해진다  
일단 표준 입출력 대상이 위 다이어그램과 같이 Disk 인 경우를 생각해보자.  
그러면 Disk와의 속도 차이가 분명히 날테니까 이를 Buffer를 사용해서 완화할 수 있다.  
따라서 Buffer 유무에 따라 속도 차이가 난다.  
  
<br>
  
![image](https://github.com/PhysicksKim/TIL/assets/101965836/2dcee277-f423-47a0-94c4-ce87fce237a4)  

그러면 의문이 들 수 있다.  

> Disk가 아니라 CLI 프로세스 상에서 입출력을 받는 거 아냐?  

물론이다. 그러면 Disk가 아니라 CLI 프로세스가 띄워져 있는 Memory 로 바꿔서 생각해볼까?  
  
위 다이어그램과 같이 Memory(CLI가 띄워진 곳)로 바꿔도 다를 바 없다.  
CPU는 Memory 보다 월등하게 빠르니까  
이 또한 Buffer가 유의미해질 수 있다는 것이다.  
  
따라서 BufferedReader 를 통해서 Buffer를 적용시켜야 더 빠르다.    
  
<br><br><br>  

# 각종 포스팅 참고    

[참고1](https://dlee0129.tistory.com/238)  
[참고2](https://stackoverflow.com/questions/2231369/scanner-vs-bufferedreader)  
  
구체적으로 Scanner가 Buffer를 전혀 사용하지 않는 것은 아니다.  
다만 버퍼 크기의 차이가 있다.   
Scanner는 1KB, BufferedReader 는 8KB의 버퍼를 가진다.  
또한 Scanner는 Parsing에 많은 시간을 보내므로 느려진다는 것.
