# SQL 쿼리문 기초 학습 노트

Inflearn 김영한님 Spring DB 접근기술 배우기 전  
SQL 쿼리문 문법부터 배우기 위해 시작  
  
Programmers에 있는 SQL 문제 따라가면서 배움   
[Programmers SQL 문재](https://programmers.co.kr/learn/courses/30)  
  
  
# count(\*)
count는 이름 그대로 **해당 요소가 몇 개나 있는지** 갯수를 세어주는 명령이다  
```SQL
SELECT count(*) FROM ANIMAL_INS
```
위 예시는 ANIMAL_INS 테이블에 요소들이 모두 몇 개 있는지, 조건 없이 총 갯수를 세는 명령이다  

```
SELECT count(DISTINCT NAME) FROM ANIMAL_INS
```
위 예시는 NAME 중복 없이 총 몇 개의 요소들이 있는지 세는 명령이다.  

```
SELECT DISTINCT count(*) FROM ANIMAL_INS
```
위 예시는 모든 요소가 일치하는 중복 없이 총 몇 개의 요소들이 있는지 세는 명령이다.  
  
