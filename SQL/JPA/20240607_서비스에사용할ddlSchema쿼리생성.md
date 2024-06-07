# 서비스 에서는 ddl-auto : validate 사용   


```
spring:
  jpa:
    hibernate:
      ddl-auto: create-drop
```  
  
실 서비스에서는 ddl-auto 를 create-drop 으로 사용하면 당연히 안된다.  
앱을 재실행 할때마다 db 가 날아가면 안되니까.  
따라서 ddl 에 의한 자동 db table 생성을 사용하면 안되고  
직접 table 생성 schema 를 서비스 DB에 쿼리를 쳐서 추가해줘야 한다.  
  
<br><br>  

# JPA Table 생성 쿼리 추출
JPA entity 들의 table 생성 쿼리를  
일일이 java 코드를 보면서 상상해서 쿼리를 입력하는건 비효율 적이다.  

그래서 javax persistence 설정 중에서 schema-generation 이라는 설정이 있다.  
schema-generation 을 사용하면 테이블 생성 쿼리를 파일로 추출할 수 있다.  

### Spring application.yml 에 설정 추가  
```
spring:
  jpa:
    properties:
      javax:
        persistence:
          schema-generation:
            scripts:
              create-target: src/main/resources/schema.sql
              action: create
```

schema-generation 에서 위와 같이 설정을 해주고  
Spring 을 한번 run 해주면, 자동으로 .sql 파일이 위 <code>create-target</code> 경로와 파일명으로 생성된다.  
간편하게 아래와 같이 생성된 sql 쿼리를 사용하면 된다.  
  
```sql
create table leagues (
    current_season integer,
    league_id bigint not null,
    korean_name varchar(255),
    logo varchar(255),
    name varchar(255) not null,
    primary key (league_id)
) engine=InnoDB;
```

<br><br>  

# 추가 설정 : Dialect 

```
jpa:
  properties:
    hibernate:
      dialect: org.hibernate.dialect.MariaDBDialect
```

서비스 db dialect 에 맞춰서 SQL 을 생성하도록 하자.  
