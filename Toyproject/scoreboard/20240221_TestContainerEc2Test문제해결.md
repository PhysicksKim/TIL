# 문제 발생  
EC2 환경에서 TestContainer 라이브러리를 사용한 Test Class 들이 모두 실패했다  

```
./gradlew test
Starting a Gradle Daemon (subsequent builds will be faster)

> Task :test

MemoryRedisRemoteCodeServiceTest > Remote code 를 생성하고 유효성을 검증 시 통과한다. FAILED
    java.lang.ExceptionInInitializerError at NativeConstructorAccessorImpl.java:-2
        Caused by: java.lang.IllegalStateException at DockerClientProviderStrategy.java:277

MemoryRedisRemoteCodeServiceTest > Remote code 를 생성하고 subscriber 를 추가하고 삭제합니다. FAILED
    java.lang.NoClassDefFoundError at NativeConstructorAccessorImpl.java:-2
        Caused by: java.lang.ExceptionInInitializerError at NativeConstructorAccessorImpl.java:-2

MemoryRedisRemoteCodeServiceTest > Remote code expire 시간을 설정하고 유효성을 검증합니다. FAILED
    java.lang.NoClassDefFoundError at NativeConstructorAccessorImpl.java:-2
        Caused by: java.lang.ExceptionInInitializerError at NativeConstructorAccessorImpl.java:-2

RedisContainerIntegrationTest > exampleTest() FAILED
    java.lang.NoClassDefFoundError at NativeConstructorAccessorImpl.java:-2
        Caused by: java.lang.ExceptionInInitializerError at DockerClientProviderStrategy.java:277
```

NoClassDefFoundError , ExceptionInInitializerError 를 보면  
초기화 과정에서 에러가 발생한 듯 한다.  
  
로컬 개발환경에서는 정상적으로 테스트에 성공했고, TestContainer 를 제외한 다른 테스트는 성공했다.  
  
<br><br>  
  
# 해결 과정  
  
### 우선 EC2 에 ssh 로 접속  
   
1. Docker 정상 설치 확인
### 도커 설치 확인 - 버전 체크  
```
docker -v
```
ec2 에서 도커 버전 체크로 도커가 정상적으로 설치되어 있는지 확인한다   
testcontainer 는 도커를 사용하므로 도커 설치가 필요하다    

<br><br>  
  
2. Docker 실행 체크
```
systemctl status docker.service
```
도커가 실행중인지 체크한다.  
실행중인 경우 <code>Active(running)</code> 를 확인할 수 있다.  
```
   Active: active (running) since 수 2024-02-21 13:50:53 KST; 4s ago
```

만약 실행중이 아닌 경우 아래 명령어로 ec2 에서 도커를 실행하자.  
```
sudo service docker start
```

<br><br>  

3. Docker 그룹에 ec2-user 가 속하는 지 체크
gradlew test 가 Docker 에 접근 할 수 있는 권한이 있어야 한다.
  
> 리눅스 그룹 개념에 대해 학습하자
  
따라서 Docker 그룹에 ec2-user 사용자를 추가해주자.  
  
```
sudo usermod -aG docker ec2-user
```

그룹에 추가한 다음, 재부팅 해서 문제가 해결됐다.  
  
