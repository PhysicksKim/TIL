[참고 1](https://rastalion.me/postgresql%EA%B3%BC-mariadb%EC%9D%98-%EC%82%AC%EC%9D%B4%EC%97%90%EC%84%9C%EC%9D%98-%EC%84%A0%ED%83%9D/)   
[참고 2](https://technfin.tistory.com/entry/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EB%B9%84%EA%B5%90-%EC%98%A4%EB%9D%BC%ED%81%B4-MySQLMariaDB-PostgreSQL)  
  
[참고 3-1](https://velog.io/@tennfin1/MariaDBMySQL-vs-PostgreSQL-%EC%84%B1%EB%8A%A5%EC%B0%A8%EC%9D%B4%EA%B0%80-%EC%9E%88%EC%9D%84%EA%B9%8C-15pc4smk)  
[참고 3-2](https://velog.io/@tennfin1/MariaDBMySQL-vs-PostgreSQL-23-%EC%9D%BD%EA%B8%B0%EC%97%90%EC%84%9C-%EC%96%B4%EB%96%A4-%EC%B0%A8%EC%9D%B4%EA%B0%80-%EC%9E%88%EC%9D%84%EA%B9%8C)  
[참고 3-3](https://velog.io/@tennfin1/MariaDBMySQL-vs-PostgreSQL-33-%EB%B6%80%EA%B0%80-%EA%B8%B0%EB%8A%A5%EC%97%90%EC%84%9C-%EC%96%B4%EB%96%A4-%EC%B0%A8%EC%9D%B4%EA%B0%80-%EC%9E%88%EC%9D%84%EA%B9%8C-indexing)    
  
  
# 요약  
1. MariaDB 는 단순 쿼리(특히 단순 읽기)에 우수, PostgreSQL 은 복잡한 쿼리에 우수. (ex. 복잡한 쿼리나 JOIN)
2. MariaDB 는 싱글 코어로 쿼리 수행, PostgreSQL 은 멀티 스레드를 고려한 쿼리 수행 설계가 이뤄져 있음.
3. PostgreSQL 의 신뢰성과 안정성이 더 높음
4. PostgreSQL 은 Update 가 더 늦음. update 시 delete 후 insert 되도록 설계되어 있다고 함
> "참고 2" 에서 발췌한 내용인데, 어느정도 성능 차이가 있는지는 불확실함.
5. PostgreSQL 은 JSON XML 같은 비정형 데이터에 상대적으로 적합 (자체 지원)    
  
# 결론  
특히 **참고 3** 에서 보여준 그래프를 보면  
성능이나 인덱싱 면에서 PostgreSQL 이 훨씬 좋은 모습을 보여주는 것 같다.  
  
MariaDB 같은 경우 MYSQL 과 유사하고 DB Migration(타 DB 로 전환) 시 이점이 있다고는 하는데  
이 점은 내 프로젝트에서는 당장에 큰 고려사항이 아닌 것 같다.  
