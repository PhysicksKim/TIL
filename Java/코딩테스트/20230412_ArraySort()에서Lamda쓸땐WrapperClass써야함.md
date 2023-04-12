# 뭔소리?
: Arrays.sort() 에서 lambda 넘길때는, array가 WrapperClass 여야 한다  
  
## 예시 : int배열 내림차순 정렬  
  
### 에러 발생  
```java
int[] primitiveArr = new int[4]; // 기본형(Primitive) 배열   
  
// Arrays.sort(primitiveArr, (i1, i2) -> i2-i1); // 에러 발생
```
  
### 해결 - Wrapper Class 배열로    
```java
Integer[] wrapperArr = new Integer[4]; //
  
Arrays.sort(wrapperArr, (i1, i2) -> i2-i1); 
```
 
<br><br>  
 
# 왜? : lambda는 곧 Comparator인데, primitive type은 제네릭을 쓸 수 없다  
  
무슨 말인가 하면  
  
```java
@FunctionalInterface
public interface Comparator<T> {
}
```
이렇게 Comparator에서 Generice T에 들어갈 type이 딱 정해져야 하는데  
primitive type은 들어갈 수 없다  
Comparator\<int> 이렇게는 안된다는 말이다  
  
<br>  
  
### Arrays.sort(Array, Lambda) 의 소스코드  

```java
public static <T> void sort(T[] a, Comparator<? super T> c) {
  sort(a, 0, a.length, c);
}
```

```java
public static <T> void sort(T[] a, int fromIndex, int toIndex,
  Comparator<? super T> c) {
    
    ...
      if (Collections.compare(a[i - 1], a[i], c) > 0) {
        ...
      }
    ...
}
```
  
이렇게 Comparator를 활용해서 정렬을 정의하는데  
primitive는 generic에 들어갈 수 없으니까 문제가 발생한다  
  
> ### 왜 primitive는 generic에 못 들어가나요?  
> Java에서 generic은 하위호환성을 위해서   
> Class 파일로 변환하면 generic erasure를 통해 Object로 다 치환된다  
>   
> 무슨 말인지 모르겠다면  
> 그냥 Generic으로 (T genericObj) 이렇게 되어있다면  
> 컴파일 후에 (Object genericObj) 이렇게 다 바뀐다는 뜻이다  
>    
> 그런데 모든 객체들은 Object로 담을 수 있지만  
> primitive type은 Object로 담을 수 없으니까  
> 결국 primitive type은 generic에 들어가지 못한다  
  
<br><br><br>  

---

<br><br><br>  

# 요약  
<Code>int\[\]</Code> 배열 같은 기본형 배열은  
<Code>Arrays.sort(배열, lambda)</Code> 처럼 lambda랑 같이 넘기지 못한다  
왜냐하면 <Code>Comparator\<T></Code> Generic 에다가 기본형을 못 넣기 때문이다  
  
따라서  
<Code>Integer\[\]</Code> 이렇게  
Wrapper Class 배열로 바꾼다음   
<Code>Arrays.sort(배열, lambda)</Code> 이렇게 해줘야 한다  
<Code>Comparator\<Integer\></Code> 는 가능하니까  
