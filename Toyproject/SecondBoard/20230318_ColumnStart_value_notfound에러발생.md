# 에러 발생   

```
spring:
  jpa:
    hibernate:
    // ddl-auto: create
       ddl-auto: update
```
ddl-auto 옵션을 create 에서 update 로 바꿨더니 아래와 같은 에러가 났다  

```
2023-03-18 21:17:18.168  WARN 37173 --- [  restartedMain] o.h.engine.jdbc.spi.SqlExceptionHelper   : SQL Error: 42122, SQLState: 42S22
2023-03-18 21:17:18.168 ERROR 37173 --- [  restartedMain] o.h.engine.jdbc.spi.SqlExceptionHelper   : Column "start_value" not found [42122-214]
2023-03-18 21:17:18.171 ERROR 37173 --- [  restartedMain] j.LocalContainerEntityManagerFactoryBean : Failed to initialize JPA EntityManagerFactory: [PersistenceUnit: default] Unable to build Hibernate SessionFactory; nested exception is org.hibernate.exception.SQLGrammarException: Unable to build DatabaseInformation
2023-03-18 21:17:18.173  WARN 37173 --- [  restartedMain] ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Invocation of init method failed; nested exception is javax.persistence.PersistenceException: [PersistenceUnit: default] Unable to build Hibernate SessionFactory; nested exception is org.hibernate.exception.SQLGrammarException: Unable to build DatabaseInformation
```

```
2023-03-18 21:17:18.201  INFO 37173 --- [  restartedMain] ConditionEvaluationReportLoggingListener : 
Error starting ApplicationContext. To display the conditions report re-run your application with 'debug' enabled.

2023-03-18 21:17:18.220 ERROR 37173 --- [  restartedMain] o.s.boot.SpringApplication               : Application run failed

org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Invocation of init method failed; nested exception is javax.persistence.PersistenceException: [PersistenceUnit: default] Unable to build Hibernate SessionFactory; nested exception is org.hibernate.exception.SQLGrammarException: Unable to build DatabaseInformation
  ...
Caused by: javax.persistence.PersistenceException: [PersistenceUnit: default] Unable to build Hibernate SessionFactory; nested exception is org.hibernate.exception.SQLGrammarException: Unable to build DatabaseInformation
  ...
Caused by: org.hibernate.exception.SQLGrammarException: Unable to build DatabaseInformation
	...
Caused by: org.h2.jdbc.JdbcSQLSyntaxErrorException: Column "start_value" not found [42122-214]

종료 코드 0(으)로 완료된 프로세스
```
  
## 정상동작 : create, create-drop, none / 에러 : update, validate 
  
물론 개발 환경에서 create 를 쓰다가 배포에서만 none 으로 바꾸면 되겠지 라고 할 수 있는데  
  
### 아니! 왜 뭐떄문에 update 랑 validate 만 안되는건데???  

> 해결 방법만 보려면 가장 아래의 # 해결 방법 으로 곧바로 이동  

<br><br>  

---

<br><br>  

# 해결 과정  

```
Column "start_value" not found [42122-214] 
```

내 Entity field 중에는 start_value라는 게 없는데?  
  
그리고 sql 쪽에서 에러가 나는거고, update 랑 validate 에서만 에러가 나니까  
먼저 아래와 같이 생각을 했다      
  
1. 일단 처음 앱이 실행될 때 ddl-auto 때문에 나는 에러다    
2. start_value 라는 이름을 쓰는 column은 없다   
  
### start_value 라는 이름 안쓰는데 왜 자꾸 Column "start_value" not found 가 뜰까?  
  
이에 대해 2가지 원인 중 하나라 생각했다      
  
1. 예약어 때문에 syntax error    
2. db 설정과 관련된 schema 에 문제가 생김  
  
### 1. 예약어 때문에 syntax error  
Entity 중 하나가 User 였는데  
h2 docs 보니까 예약어(Reserved Words) 중에서  
User가 있었다  
  
<img width="714" alt="image" src="https://user-images.githubusercontent.com/101965836/226108834-399a6951-8891-4b17-a6f1-674da52532ea.png">    
<img width="499" alt="image" src="https://user-images.githubusercontent.com/101965836/226108856-2b833ab9-1931-4931-be2d-6997dbf225df.png">   
  
그래서 혹시나 해서 아래와 같이 entity table 이름을 바꿔봤다  

```java
@Entity
@Data
@Table(name = "Users")
public class User {
```
  
하지만 이렇게 해도 여전히 똑같았다    
아마 user 문제는 아닌 것 같다  
  
### 2. db 설정과 관련된 schema에 문제가 생김  

아마 hibernate 에서 ddl-auto : update 과정에서  
```
select start_value from 뭐뭐뭐 
```
이런 식으로 start_value 라는 column 을 쓰는 쿼리가 나갔으니까 저런 에러가 났지 않았을까?  
  
그러니까 일단 h2에 start_value 가 있는지 찾아봤다.  
  
<img width="620" alt="image" src="https://user-images.githubusercontent.com/101965836/226110013-85724c43-feac-408b-8de4-43de611a281b.png">  
<img width="406" alt="image" src="https://user-images.githubusercontent.com/101965836/226110006-a1d3e293-b18c-4c46-a019-08101c72ea01.png">  
  
이렇게 H2 문서에서 찾아보니 SEQUENCES.start_value 가 있다  
    
<br><br>  
    
### 근데, 내 db 에는 SEQUENCES.start_value 가 없다  
  
그러면 버전 문제인가?  
    
근데 **h2 공식 문서에 구버전 document가 없다**     
그래서 2.x 버전이랑 1.4 버전이랑 document를 비교해 볼 수 없었다  
  
그냥 일단 아래와 같이 gradle dependency 에서 h2 버전을 명시해줬다  
  
```  
dependencies {
  ...
//	runtimeOnly 'com.h2database:h2'
	runtimeOnly 'com.h2database:h2:1.4.200'
  ...
}
```
  
이렇게 하니까  
되네?  
    
### 해결 성공!  
  
<br><br>  

---

<br><br>  

# 해결 방법  

### 원인    
```  
Column "start_value" not found [42122-214]   
```  
최신 버전에는 INFORMATION_SCHEMA.SEQUENCES 테이블에 start_value 라는 이름으로 column 이 있는데    
내가 사용한 1.4.200 버전에는 start_with 라는 이름이었다.    
    
그리고 결정적으로    
build.gradle 에서  
```
dependencies {
  ...
	runtimeOnly 'com.h2database:h2'
  ...
}
```  
   
이렇게 h2 dependency 버전을 명시해주지 않아서    
최신 버전을 기준으로 dependency가 잡혔고     
hibernate 는 ddl-auto : update 에서 최신 버전을 기준으로     
start_value 라는 이름으로 column에 접근했다가 에러가 난 것.   
    
<br><br>  
   
### 해결방안   
그냥 h2 버전을 명시해주면 된다    
  
```
dependencies {
  ...
//	runtimeOnly 'com.h2database:h2'
	runtimeOnly 'com.h2database:h2:1.4.200'
  ...
}
```     
  
해결 완료   

<br>

---

<br>  

# 추가 - 구버전 1.4.200 문서  
[H2 1.4.200 문서](https://web.archive.org/web/20210315211844/http://h2database.com/html/main.html)  
  
<img width="817" alt="image" src="https://user-images.githubusercontent.com/101965836/226113463-50a2d3dc-e9ff-4c66-ad3e-605dd2d1a7d3.png">  

Chat GPT 한테 물어보니까 알려주네  
미쳤다  
