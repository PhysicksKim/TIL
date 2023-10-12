# 사용하면 안되는 것들  


## 1. [NoOpPasswordEncoder](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/crypto/password/NoOpPasswordEncoder.html)
암호화 하지 않는 PasswordEncoder. 특별히 테스트 과정에서 쓸 거 아니면 쓸 이유 없음.  
  
## 2. [StandardPasswordEncoder](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/crypto/password/StandardPasswordEncoder.html)   
암호화는 하지만 충분히 안전하지 않아서 Deprecated 됐다.
이는 현대 컴퓨팅 파워로 충분히 브루트 포스 어택을 할 수 있는 정도의 연산이기 때문이다.

> ### [공식 문서](https://docs.spring.io/spring-security/reference/features/authentication/password-storage.html)  
> In modern times, we realize that cryptographic hashes (like SHA-256) are no longer secure.  
> The reason is that with modern hardware we can perform billions of hash calculations a second.  
> This means that we can crack each password individually with ease.    

> ### [Spring API Docs - StandardPasswordEncoder](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/crypto/password/StandardPasswordEncoder.html)   
> A standard PasswordEncoder implementation that uses SHA-256 hashing with 1024 iterations and a random 8-byte random salt value.  
> Digest based password encoding is not considered secure.
   
## 3. [Pbkdf2PasswordEncoder](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/crypto/password/Pbkdf2PasswordEncoder.html)     
사실 Pbkdf2 방식은 사용이 되기는 하는데 권장하지는 않는다고 한다.    
그 이유는 PBKDF2는 일단 뒤에 말할 BCrypt, SCrypt, Argon2 같은 방식보다 "한번의 연산이 덜 무겁다"는 이유에서다.   
**"한번의 연산이 덜 무겁다"**는 말은 **"하나의 계산을 할 때 얼마나 RAM이 필요한가?"** 라는 말이다.     
   
정확히는 모르겠지만, [PBKDF2 Wikipedia](https://en.wikipedia.org/wiki/PBKDF2) 의 설명을 보고 내가 이해한 내용은 이렇다.      
단순히 연산 횟수를 늘려서 시간을 늘릴 수 있겠지만,   
계산 한번이 적은 메모리로도 가능하기 때문에 GPU 같은 걸로 한번에 병렬 연산이 가능하다.   
   
## (내가 이해한) Pbkdf2가 취약한 부분 설명  
> Pbkdf2에 대해 이해한 내용을 뇌피셜로 설명한 부분입니다.  
> 아마도 실제 브루트 포스 공격의 설계 방식과 매우 다를겁니다.
> 그냥 설명을 위한 비유 정도로 이해해주세요       
  
Pbkdf2 는 위키피디아 설명에 따르면  
> ASIC(Application-Specific Integrated Circuits)이나 GPU(Graphics Processing Units)를 이용한 브루트포스 공격에 상대적으로 취약하다.

라고 한다.  
일단 Pbkdf2 는 가벼운 연산을 여러번 반복하도록 해싱 알고리즘이 되어있다. (사용하는 SHA 해싱 함수가 애초에 "가볍고 빠른" 해싱 알고리즘이다)  
따라서 SHA 정도 연산을 돌릴 수 있는 CPU를 여럿 가지고 있는 GPU를 사용한다면 이를 브루트 포스로 뚫을 수 있다는 것이다.   
  
GPU 를 이용한 브루트포스를 막기 위해서 연산 한번에 필요한 메모리 량을 늘린다고 하는게 뭔가 생각해봤는데   
아마도 아래와 같은 식이지 않을까 싶다.  

### 메모리가 적게 필요한 경우 
암호화된 비밀번호 역연산 1회에 아래와 같다은 자원이 필요하다고 해보자.     
```
메모리 1KB + cuda core 4개 + 1초  
```
  
RTX 4090 기준으로 cuda core가 16000개 정도이니 4000개씩 병렬 연산이 가능하고  
4000개씩 병렬 연산이 가능해지니까 25,000초 밖에 안걸리게 된다.  

### 메모리가 많이 필요한 경우  
여기에다가 계산 한번을 위해 필요한 메모리 양을 극단적으로 늘려보자  
```
메모리 32MB + cuda core 4개 + 1초  
```
이러면 계산 한번에 128,000MB = 125GB 가 필요하다.  
따라서 RTX 4090 GPU Memory 24GB를 초과하게 된다.   
단순히 메모리 사용량 자체를 늘리는 것만으로도 이렇게 브루트 포스 공격이 쉽지 않도록 만들 수 있다.  
  
<br><br><br>  
  
# Production에서 사용 가능한 password encoder 들  
  
1. BCryptPasswordEncoder
2. SCryptPasswordEncoder
3. Argon2PasswordEncoder
  
  
위 방식들은 간략히 설명하자면  
Spring에서는 현재 BCrypt를 기본 방식으로 채택하고 있으며  
1번 부터 3번으로 갈 수록 더 많은 자원을 사용한다고 보면 된다.  

1. BCrypt : 높은 CPU 연산 능력
2. SCrypt : 높은 CPU 연산 능력 + 많은 메모리 사용  
3. Argon2 : 높은 CPU 연산 능력 + 많은 메모리 사용 + 다중 스레드 요구
  
따라서 위 3가지 방식은 그냥 보안성과 사용해야 하는 리소스 사이의 trade-off 관계라고 보면 된다.  
  
