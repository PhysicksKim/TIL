# Quartz 에서 알맞는 query 제공해줌  

### 환경  

- Intellij
- Spring Boot 3
- Spring Quartz
- MariaDb(innodb)  
  
`tables_mysql_innodb.sql` 라는 파일을 검색하면 innodb 용 sql 스크립트 파일을 찾을 수 있다.  
  
이러한 스크립트 파일들을 gradle라이브러리 목록에서 직접 찾으려면       
`org.quartz-scheduler:` `/org/quartz/impl/jdbcjobstore` 에서 `tables_**.sql` 파일들을 찾을 수 있다.  
각종 다양한 DB에 맞는 스크립트 파일을 Quartz 에서 제공해준다.  
  
<br><br>  

# Quartz Scheduler Github Repository 에서 직접 확인  

[quartz/quartz-scheduler](https://github.com/quartz-scheduler/quartz) 레포지토리에서  
`quartz/src/main/resources/org/quartz/impl/jdbcjobstore` 경로에서도 Schema 생성을 위한 `table_**.sql` 파일들을 볼 수 있다.  
[github repository 의 sql script 파일 목록으로 이동](https://github.com/quartz-scheduler/quartz/tree/main/quartz/src/main/resources/org/quartz/impl/jdbcjobstore)  
  
내 춘시티 백엔드의 경우 mariadb 이므로 innodb 용 sql 스크립트 파일을 사용하면 된다.  
  
