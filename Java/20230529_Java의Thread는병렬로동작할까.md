# Thread 말고 Multi Core 사용하고 싶은데...  
  
궁금한 게 생겼다.  

일단   
Multi Thread와 Multi Process의 차이는 알고있다.  
[▶ 20230110_스프링입문을위한자바객체지향의원리와이해.md - Multi Thread vs Multi Process](https://github.com/PhysicksKim/TIL/blob/72f6c9019398324119124bfc6f524024f40c263c/Books/20230110_%EC%8A%A4%ED%94%84%EB%A7%81%EC%9E%85%EB%AC%B8%EC%9D%84%EC%9C%84%ED%95%9C%EC%9E%90%EB%B0%94%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5%EC%9D%98%EC%9B%90%EB%A6%AC%EC%99%80%EC%9D%B4%ED%95%B4.md#%EB%A9%80%ED%8B%B0-%EC%8A%A4%EB%A0%88%EB%93%9C--%EB%A9%80%ED%8B%B0-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4)  
  
또한  
병렬성(Parallelism)과 동시성(Concurrency)의 차이도 알고있다.  
[▶ 20220504_동시성병렬성.md](https://github.com/PhysicksKim/TIL/blob/72f6c9019398324119124bfc6f524024f40c263c/Java/20220504_%EB%8F%99%EC%8B%9C%EC%84%B1%EB%B3%91%EB%A0%AC%EC%84%B1.md)  
  
<br>  
  
### 의문    
  
> Java에 Multi Thread 밖에 못봤는데... Multi core를 활용하려면 어떻게 해야하지?  
> 다른 Jar 파일을 또 실행해서 프로세서간 통신을 해야하나?  
  
이었다.  
  
<br><br>
  
[참고](https://stackoverflow.com/questions/28937379/does-java-actually-run-threads-in-parallel?rq=3)  
  
# 결론  
일단 Java에서는 정확히 Multi Core를 써라고 지정할 방법은 딱히 없다.  
또한 JVM에서 Multi Core를 활용한다는 스펙또한 찾기 못했다.  
  
다만 실험 결과나 StackOverflow 답변들을 종합해본 결과  
어쨌거나 Parallelism을 통해서 Multi Core를 사용해 연산 속도를 향상시킬 목적이라면  
그렇게 동작한다고 결론을 내렸다.  
  
아래의 예시 코드에서도 볼 수 있겠지만  
Thread를 여럿 생성해놓고 무거운 연산을 돌려서 비교해보면   
Multi Thread를 적절히 잘 쓰면  
Single Thread일 때보다 더 빠르게 연산하는 것을 알 수 있다  
  
따라서  
그냥 Java에서 Multi Thread로 코딩해놓으면  
이를 OS가 알아서 스케쥴링해서 Multi Core를 활용해서 동작하는 것 같다.  
(아쉽지만 확실한 레퍼런스는 찾지 못했고, 실험과 질문답변으로만 얻은 결론이다)  
  
<br>  
  
## 실험 코드  

### 싱글 스레드로 연산  
```java
public class SingleThreadExample {

    public static void main(String[] args) {

        long start = System.currentTimeMillis();

        long sum = 0;
        for (long i = 0; i <= 1_000_000_000; i++) {
            sum += i;
        }

        System.out.println("Result: " + sum);
        System.out.println("Elapsed time: " + (System.currentTimeMillis() - start) + "ms");
    }
}
```
  
> ### 결과   
> Result: 500000000500000000    
> Elapsed time: 372ms   
   
<br><br>  
  
### 멀티 스레드로 연산  
  
```java
public class MultiThreadExample {

    public static void main(String[] args) throws InterruptedException {

        long start = System.currentTimeMillis();

        SumThread[] threads = new SumThread[10];
        final int multiple = 100_000_000;
        for(int i = 0 ; i < threads.length ; i++) {
            threads[i] = new SumThread(i*multiple, (i+1)*multiple);
        }

        for(int i = 0 ; i < threads.length ; i++) {
            threads[i].start();
        }
        for(int i = 0 ; i < threads.length ; i++) {
            threads[i].join();
        }

        long totalSum = 0;
        for(int i = 0 ; i <threads.length ; i++) {
            totalSum += threads[i].getSum();
        }

        System.out.println("Result: " + totalSum);
        System.out.println("Elapsed time: " + (System.currentTimeMillis() - start) + "ms");
    }

    static class SumThread extends Thread {
        private long from, to, sum = 0;

        public SumThread(long from, long to) {
            this.from = from;
            this.to = to;
        }

        @Override
        public void run() {
            for (long i = from; i <= to; i++) {
                sum += i;
            }
        }

        public long getSum() {
            return sum;
        }
    }
}
```
  
> ### 결과   
> Result: 500000005000000000  
> Elapsed time: 323ms  
   
<br><br>  
  
## 결과 비교 

|싱글 스레드 연산|멀티 스레드 연산|
|---|---|
|372ms|323ms|

실험 결과를 보면 멀티 스레드로 연산한 게 더 빠른 것을 알 수 있다.  
  
다만 10개의 Thread를 사용했다고 해서 10배 빠르지는 않은 것을 볼 수 있다.  
이는 Thread를 만들고 값을 교환하고 메모리 공간을 생성하는 등에서 많은 오버헤드가 발생하기 때문이다.  
그러므로 무조건 Multi Thread를 쓴다고 빨라지지 않는다.  
  
## 오히려 Multi Thread가 더 느릴 수 있다  
  
실제로 위 Multi Thread 코드를 아래와 같이 바꿔서 실행해보면 더 느린 것을 알 수 있다.  

```java
public class MultiThreadExample_slower {
    public static void main(String[] args) throws InterruptedException {

        long start = System.currentTimeMillis();

        SumThread thread1 = new SumThread(0, 500_000_000);
        SumThread thread2 = new SumThread(500_000_001, 1_000_000_000);

        thread1.start();
        thread2.start();

        thread1.join();
        thread2.join();

        long totalSum = thread1.getSum() + thread2.getSum();

        System.out.println("Result: " + totalSum);
        System.out.println("Elapsed time: " + (System.currentTimeMillis() - start) + "ms");
    }

    static class SumThread extends Thread {
        private long from, to, sum = 0;

        public SumThread(long from, long to) {
            this.from = from;
            this.to = to;
        }

        @Override
        public void run() {
            for (long i = from; i <= to; i++) {
                sum += i;
            }
        }

        public long getSum() {
            return sum;
        }
    }
}
```

동일하게 0부터 1_000_000_000 까지 더하는 작업이지만   
앞전의 예제 코드에서는 10개의 스레드를 활용했고  
위의 예제 코드에서는 2개의 스레드만을 사용했다.
  
```
Result: 500000000500000000
Elapsed time: 794ms
```
   
그 결과 멀티 스레드를 통한 병렬의 이점보다  
멀티 스레드로 인한 오버헤드가 더 커져서  
오히려 한참 더 느린 794ms가 걸렸다  
  
|싱글 스레드|멀티 스레드 (10스레드)|멀티 스레드 (2스레드)|
|---|---|---|
|372ms|323ms|794ms|
  
<br><br> 
  
# 멀티 스레드가 병렬성의 이점을 가지려면  
  
1. 알고리즘에서 연산 부하가 큰 부분을 적절히 잘 나눠야 한다   
2. 오버 헤드보다 병렬성이 이점이 더 크도록 스레드를 잘 설계해야 한다   
   
위 두 사항을 고려해야한다   
   
이는 단순히 코드의 문제뿐만이 아니라 OS와 하드웨어도 같이 고려해야 하므로   
멀티 스레드를 통한 병렬 프로그래밍은 쉽지 않은 영역임을 알 수 있다.   
   
## 일단은 동시성에 초점을 맞추자   
웹 개발에서 병렬성을 챙길만큼 무거운 연산을 할 일은 잘 없다.    
정말 그렇게 무거운 연산이 있다면(ex. AI) 이는 따로 그 분야의 전문가가 담당할거라 생각한다.    
초보 개발자 입장에서 Thread는 병렬성 보다는 동시성에 초점을 맞추도록 하자.   
