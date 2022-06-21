# SQL 쿼리문 기초 학습 노트

Inflearn 김영한님 Spring DB 접근기술 배우기 전에  
SQL 쿼리문 문법부터 학습해야할 필요성 느끼고  
쿼리문 기초부터 차근히 시작  
  
  
## 학습 사이트
1. [SQL ZOO](https://sqlzoo.net)
2. [Programmers - SQL](https://programmers.co.kr/learn/courses/30)  
  
SQL ZOO가 레벨순으로 기본 설명과 기본 문제를 줘서  
진짜 쌩기초 노베이스상태에서 배우기 적합함  
  
SQL ZOO 부터 한 후에  
다른 문제풀이 사이트 이용을 추천함.  
  
# 0 SELECT basics
진짜 기본적인 SELECT와 WHERE, IN, BETWEEN a AND b 내용  

```SQL
SELECT population FROM world
  WHERE name = 'Germany'
```

```SQL
SELECT name, population FROM world 
  WHERE name IN ('Sweden', 'Norway', 'Denmark')
```

```SQL
SELECT name, area FROM WORLD 
  WHERE area BETWEEN 200000 and 250000
```

# 1 SELECT name
LIKE와 와일드카드(\% , \_)에 대해 알려줌  
  
와일드카드 \%는 **'아무 문자나 들어올 수 있음. 문자가 안들어 와도 됨'** 이라는 뜻이다 
무슨 말이냐면  

```
SELECT name FROM world
  WHERE name LIKE '%a%a%a%'
```
이렇게 했을 때 name이 aaa 인 값도 인정된다는 말임  
  
  
  
  
  
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
  
