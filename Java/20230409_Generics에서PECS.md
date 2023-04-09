[참고1](https://stackoverflow.com/questions/2723397/what-is-pecs-producer-extends-consumer-super)  
[참고2](https://mangkyu.tistory.com/241)  
   
# PECS (producer-extends, consumer-super)    
   
generics에서는 extends와 super를 쓸 수 있다   
   
<br><br>   
   
# PECS는 직관적이지 않다   
     
직관적이지 않다.    
코드 작성중에 PECS를 생각하려면 헷갈린다     
    
> "이게... 메서드가 Producer 라는건가? Collection이 Producer 라는건가?"    
    
또 Producer랑 Consumer라고 외워둔다 하더라도   
"왜?"가 안 담겨 있으니 나중에 또 헷갈린다    
그러니까 "왜"를 외워둘 간단한 생각방법(이미지)이 필요하다  
  
<br><br>  
  
# 생각방법에 용어는 없애자  
  
공변성, 반공변성, 상한제한, 하한제한 같은 용어들을 이해하고  
와일드카드의 super와 extends에 대해 설명하는 게 올바른 수순이지만   
직관적으로 딱 언제 super 쓰고 언제 extends를 써야하는지  
곧바로 떠올릴 "생각방법"을 찾아야 한다  
   
<br>
   
## 생각 방법의 예시    
"배열"을 떠올려보자  
혹시 배열의 정의가 떠오르는 사람은 없을거다  
   
### 잘못된 이해 - 정의만 외우는 사람  
```  
배열(array)은 같은 타입의 변수들로 이루어진 유한 집합으로 정의됩니다.
```  
이런 정의가 떠오른다 하더라도  
직관적이지 않은 정의를 외우는 것은   
생각의 확장에 적합하지 않다  

<br>  
   
### 올바른 이해 - 이미지로 외우는 사람  
```  
[] , [] , [] , []  
```  
대부분 사람들은 배열을 연속된 박스 같은 이미지로 이해하고 있을거다  
  
이렇게 이미지로 이해해야지  
직접 사용할 수 있고 이해를 바탕으로 확장될 수 있다  
   
<br><br>  
  
---  
  
<br><br>  
  
# 먼저, PECS 코드 보기  
  
### extends - 메서드가 값을 빼서 쓸 때  
```java
void printCollection(Collection<? extends Parent> c) {  
    for (Parent e : c) {  // 값을 빼서 씀  
        System.out.println(e);  
    }  
}
```
  
### super - 메서드가 값을 넣어줄 때  
```java
void addElement(Collection<? super Parent> c) {
    c.add(new Parent()); // 값을 넣음
}
```
   
<br><br>     
   
---  
   
<br><br>   
  
```
Ancestor  (부모)
Parent  (기준)
Child  (자식)  
```  
  
<br>  

# 설명에 앞서, 와일드카드 제한한 타입이 기준이다    
  
```java
printCollection(Collection<? extends Parent> c)
```
  
```java
addElement(Collection<? super Parent> c) 
```
  
위와 같이 코드가 있을 때  
당연한 말이지만, Parent가 기준이다   
그런데 단순히 "기준" 이라는 표현보다는  
  
"값을 뺄려면, Parent로 받을 수 있는가?"  
"값을 넣으려면, Parent 객체를 넣을 수 있는가?"  
  
라고 기준을 잡고 생각한다는 뜻이 더 좋겠다  
  
따라서 "어떤 기준이냐?" 하면    
> 값을 빼거나 넣을 때  
> 기준 타입으로 빼거나 넣을 수 있는가?  
라고 생각하면 된다.  
  
### 그런데, 아직 부족하다  
기준을 "값을 넣고 빼는것" 으로만 생각하면 헷갈린다  
왜 헷갈리며, 그럼 도대체 뭐가 기준인가 라는 의문이 들텐데  
이에 대해서는 문서 마지막에 딱 정리할테니까  
일단은 논리를 따라가자.  
  
<br><br><br>  

---

<br><br><br>  
   
# 값을 빼서 쓸려면 extends  
왜? 값 뺀걸 <Code>Parent pr</Code> 으로 받을 수 있음   
   
### 설명  
```java
printCollection(Collection<? extends Parent> c)
```

<br>  
  
### 생각의 순서  
  
### : extends 되는 이유    
1. 값을 꺼낸다   
2. Parent p 라는 변수로 받아야한다    
3. 들어오는 컬렉션을 생각해보면, Parent, Child, ...(자식타입)... 이렇게 받아야 하는데,  
4. Parent 타입으로만 다 커버할 수 있다    
  
<br>  
  
### : super 안되는 이유   
1. 값을 꺼냈다  
2. Parent p 라는 변수로 받아야 한다  
3. 들어오는 컬렉션을 생각해보면, ...(부모타입)... , Ancestor, Parent 이렇게 받아야 하는데,  
4. Parent 타입으로 Ancestor를 받을 수 없다  
  
super로 해두면 꺼낸 값이 최대 어떤 타입인지 알 수 없어서  
Object로 받는 수 밖에 없는 지경에 이른다  
  
<br><br><br>    
  
# 값을 넣으려면 super  
왜? 부모, 부부모 컬랙션이 와도 <Code>new Parent()</Code> 를 품을 수 있음  
  
### 설명  
  
```java
addElement(Collection<? super Parent> c)   
```  
  
<br>   
  
### 생각의 순서  
  
### : super 되는 이유   
1. Parent, Child, ...(자식타입)... 을 넣으려고 한다   
2. super를 쓰면 ... , Collection\<Ancestor\> , Collection\<Parent\> 이렇게 들어온다    
3. 들어온 Collection에 Parent 객체를 넣을 수 있을까?    
4. 된다. 다 넣을 수 있으니 super가 맞다   
  
<br>  
  
### : extends 안되는 이유  
1. Parent, Child, ...(자식타입)... 을 넣으려고 한다  
2. extends를 쓰면 Collection\<Parent\>, Collection\<Child\>, ... 이렇게 들어온다    
3. 들어온 Collection에 Parent 객체를 넣을 수 있을까?  
4. 안된다. Collection\<Child\> 에 Parent 객체를 넣을 수 없으니 extends는 안된다  
  
이렇게 자식용 컬랙션에 부모가 들어가므로, extends로 해두면 안된다.   
  
> 헷갈리면   
> 넣은 값을 꺼낼 때를 생각해보자  
> Collection\<Child\>라고 한다면    
> 꺼낼 때 Child로 꺼내야 할텐데    
> ```java
> Child actuallyParent = childList.get(0);
> actuallyParent.onlyChild();
> ```
> 이렇게 child에서 정의한 메서드인 onlyChild()를 실행하면  
> parent에는 onlyChild()가 없으니 에러가 날 것이다  

  
<br><br><br>  

---

<br><br><br>  
  
# 헷갈리는 이유  
  
정리하면서 느낀건데  
헷갈리는 이유는 이거다  
  
"가능한 타입"이 둘 다 결국은 자식 타입 방향으로만 뻗어나간다.  
  
무슨 말인가 하면  
extends 하면 뺄 수 있는 값이 Parent 보다 자식이다      
super 하면 넣을 수 있는 값이 Parent 보다 자식이다  
  
근데 super라고 해두면  
Parent 보다 "위를" 넣나..? 빼나..? 라고 먼저 생각이 드니까  
이게 생각이 잘못된 길로 빠질 수 밖에 없는거다  
  
<br><br>  
  
# 그래서 생각의 기준을 "들어오는 Collection" 으로 잡아야한다  
넣고 빠지는 변수 Parent p 를 기준으로 잡으면 안된다  
    
### 1. 타입 제한하는 게 Collection이니까, Collection을 기준으로 생각해라  
### 2. 들어오는 Collection의 Generic Type이 달라지는 걸 기준으로 생각해라  
  
이렇게 2개로 기둥을 잡고 생각이 뻗어나가야지  
나중에 안헷갈릴 것 같다.  
  
<br><br><br>  
  
---
  
<br><br><br>  
    
# 정리  
  
### 들어오는 Collection 은 Parent보다 한 단계 부모나 자식으로 생각  
![image](https://user-images.githubusercontent.com/101965836/230791608-c1f576c3-2bdc-4ff1-9292-3bec21956efa.png)  
  
### 참조변수, 넣을 객체 는 Parent 기준  
![image](https://user-images.githubusercontent.com/101965836/230791671-78d2f677-ae3f-419b-ad28-37d8f7ddb415.png)  
  
### super에는 객체를 넣을 수 있다  
왜? Collection이 최소 내가 넣을려는 parent 이상임  
  
### extends에는 객체를 뺄 수 있다  
왜? Collection에서 뺀 객체가 최대 parent 니까  
Parent 타입으로 받으면 다 받을 수 있음  
