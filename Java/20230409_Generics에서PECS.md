[참고1](https://stackoverflow.com/questions/2723397/what-is-pecs-producer-extends-consumer-super)  
[참고2](https://mangkyu.tistory.com/241)  
  
# PECS (producer-extends, consumer-super)   
  
generics에서는 extends와 super를 쓸 수 있다  
  
<br><br>  
  
# 자세한 설명제외  
  
공변성, 반공변성, 상한제한, 하한제한 같은 용어들을 이해하고  
와일드카드의 super와 extends에 대해 설명하는 게 올바른 수순이지만   
직관적으로 딱 언제 super 쓰고 언제 extends를 써야하는지  
곧바로 떠올릴 "생각방법"에 대해서만 기술한다    
  
<br><br>  
  
---  
  
<br><br>  
  
# PECS 코드부터 보기  
  
### extends - 메서드가 값을 빼서 쓸 때  
```java
void printCollection(Collection<? extends MyParent> c) {
    for (MyParent e : c) {
        System.out.println(e);
    }
}
```
  
### super - 메서드가 값을 넣어줄 때  
```java
void addElement(Collection<? super MyParent> c) {
    c.add(new MyParent());
}
```
   
<br><br>     
   
---  
   
<br><br>   
   
