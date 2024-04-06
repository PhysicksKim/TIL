[참고](https://hello-capo.netlify.app/algorithm-quicksort/)  

# 동작 방식 

[설명](https://stackoverflow.com/questions/20917617/whats-the-difference-of-dual-pivot-quick-sort-and-quick-sort)  
  
1. start end 를 L R 로 선택함. L > R 인 경우 Swap
2. 이제 파트1(<L<)파트2(<R<)파트3 으로 3부분으로 나눈다.
3. 위 과정을 반복해서 quicksort 를 듀얼 피봇으로 계속 진행한다
4. 특정 값보다 작을 경우 quicksort 대신 단순한 정렬 방법 (Insertion Sort, Mergesort)으로 정렬한다. 
  
  
# Java 가 Dual Pivot Quick Sort 사용하는 이유  

> The sorting algorithm is a Dual-Pivot Quicksort  
> by Vladimir Yaroslavskiy, Jon Bentley, and Joshua Bloch.  
> This algorithm offers O(n log(n)) performance on many data sets  
> that cause other quicksorts to degrade to quadratic performance,  
> and is typically faster than traditional (one-pivot) Quicksort implementations.
  
> 블라디미르, 존, 조슈아에 의해 작성된 Dual Pivot Quicksort 알고리즘입니다. 
> 전통적인 One-Pivot Quicksort 알고리즘보다 일반적으로 더 빠르고,
> 많은 수의 데이터에 대해서 O(Nlog(N)) 의 시간 복잡도를 보입니다.
  
많은 데이터에서 좋은 성능을 보이기 때문에 채택한 것 같다.  
