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
  
  
<br>
  
---
  
---
  
<br>
  
# 집계 함수와 GROUP BY  
GROUP BY는 좀 특이하다.  
GROUP BY를 쓰기 위해서는, 앞에 SELECT 부분에서 집계 함수를 써야한다.  
또한 GROUP BY를 쓰면 집계함수를 쓰지 않은 쌩짜 column은 들어갈 수 없다.  
왜 그런지 차근히 알아보자  
  
### 집계 함수
COUNT(), SUM(), MAX(), MIN(), AVG()     
이름 그대로 특정 요소가 몇 개 있는지 카운트하거나, 합계를 구하거나, 최대 최소 평균 값을 구한다.  
이때 null은 알아서 잘 걸러서 계산해준다.  
  
### GROUP BY 란?
예를 들어   
  
|yr|subject|winner|
|---|---|---|
|1901|Chemistry|Jacobus H. van 't Hoff|
|1908|Peace|Fredrik Bajer|
  
이렇게 되어 있다면  
연도별로 몇 명이 상을 받았는지 집계하고 싶을 수 있다.  
그러면 아래와 같이  
```SQL
SELECT yr, COUNT(winner) FROM nobel GROUP BY yr
```
GROUP BY와 COUNT()를 사용하면 된다.  
  
GROUP BY는 중복된 항목들을 싹 묶어주는 역할을 한다  
예를 들어
nobel 수상자가 1901 1903 1902 1901 1903 ... 이렇게 쭉 있다고 해보자  
이걸 연도별로 1901에는 ... 가 있고, 1902에는 ... 가 있고 하는 식으로 분류하고 싶을 때가 있다  
이럴 때 1901을 쓴다.  
  
여기서 SELECT DISTINCT yr FROM nobel 과 차이점은  
DISTINCT는 
```
SELECT DISTINCT yr, COUNT(winner) FROM nobel
```
이렇게 하면 에러가 난다.  
  
왜냐하면
DISTINCT는 그냥 단순히 중복만 제거하고 yr 값만 남겨두는 거라서  
1901년에 있던 수상자들과 종목들 정보를 싸악 모아두는 GROUP BY와 다르게  
집계함수가 제대로 동작하지 않는다.  
  
### 따라서 GROUP BY는 집계함수와 짝궁이다  
GROUP BY의 의의 자체가 애초부터    
"항목 별로 (최대, 최소, 평균, 합계, 항목수)가 얼마임?"   
이므로 집계함수와 짝궁이 될 수 밖에 없다.  
  
따라서 문법적으로도 집계함수를 써야한다  
  
### count(\*)
count는 이름 그대로 **해당 요소가 몇 개나 있는지** 갯수를 세어주는 명령이다  
```SQL
SELECT count(*) FROM ANIMAL_INS
```
위 예시는 ANIMAL_INS 테이블에 요소들이 모두 몇 개 있는지, 조건 없이 총 갯수를 세는 명령이다  
  
근데 count 안에다가 DISTINCT와 \*을 넣을 수도 있다  
```
SELECT count(DISTINCT NAME) FROM ANIMAL_INS
```
위 예시는 NAME 중복 없이 총 몇 개의 요소들이 있는지 세는 명령이다.  

```
SELECT DISTINCT count(*) FROM ANIMAL_INS
```
위 예시는 모든 요소가 일치하는 중복 없이 총 몇 개의 요소들이 있는지 세는 명령이다.  
  
<br>
  
---
  
---
  
<br>
  
# Correlated Subquery (상호연관 서브쿼리)
메인 쿼리와 서브 쿼리가 뭔지에 대해 먼저 이야기해보고,  
그 다음 Correlated Subquery(상호연관 서브쿼리)가 뭔지 알아보자.  
  
### 예제 쿼리문
```SQL
SELECT continent, name, area FROM world x 
  WHERE area >= 
       ALL (SELECT area FROM world y 
                WHERE y.continent=x.continent AND area>0)
```
  
### 메인 쿼리 / 서브 쿼리  
보면 제일 처음 SELECT가 있고, 그 다음 WHERE의 ( ) 안에 SELECT가 또 있다.  
이렇게 조건절 같은 부분에서 테이블에서 어떤 요소를 '딱' 떼어다가 쓸 용도로 () 안에다가 SELECT 써서 값을 갖고 오는 녀석을 서브 쿼리라고 한다.  
  
### 그러면 Correlated는 뭔 말인가?  
결론부터 말하면,  
메인 쿼리에서 먼저 값을 가져오고, 그 값을 서브 쿼리에서 사용하기 때문에  
메인과 서브가 상호 연관이 있어서 Correlated 라고 한다.  
  
값을 갖고오고 쓴다는게 뭔말인지 예제 쿼리문을 토대로 알아보자  
  
### 예제 쿼리문에 대해 다시 자세히 보자  
```SQL
SELECT continent, name, area FROM world x 
  WHERE area >= 
       ALL (SELECT area FROM world y 
                WHERE y.continent=x.continent AND area>0)
```
![image](https://user-images.githubusercontent.com/101965836/175023946-aa3793f8-e553-47f3-adb8-9af3d9c9ff43.png)  
   
위 쿼리는 각 대륙에서 가장 면적이 넓은 나라들을 갖고오는 쿼리다.  
  
여기서 까다로운 부분이  
**'각 대륙에서'**  
라는 부분이다.  
  
만약 위처럼 서브쿼리를 쓰지 않았다면  
continent 목록을 얻은 다음  
각 대륙마다 한번씩 해서 8번 반복문으로 메인 쿼리를 요청해야한다.  
  
근데 이걸 Correlated Subquery를 사용하게 되면 위 예시처럼 간결하게 만들 수 있다.  
  
어떻게 동작하는지 차근히 다시 살펴보자.  
### 1. SELECT ... FROM world x WHERE area >= ...  
### 2. (SELECT area FROM world y WHERE x.continent = y.continent ... )  
### 3. ... WHERE area >= ALL (...)
  
이걸 다시 Correlated 동작 중심으로 설명해보면  
  
1. world 중에서 한놈을 뽑음. 얘 한놈을 이제 x라고 부름. 이제 x 중에서 area가 ★ 이상인 것들만 추출하겠다. 근데 ★이 뭐냐면...
2. ★은 x.continent = y.continent인 녀석의 area 이다.  
3. 근데 ALL()이니까 x.continent = y.continent 인 모든 케이스에 대해서 메인의 WHERE 조건을 만족해야함  
  
추가로 덧붙이면   
만약 x.continent가 Asia라고 하면  
Asia = y.continent 인 나라들의 area가 쭈우우욱 추출된다.  
그 다음 x.area >= ALL(추출된 area들) 이렇게 비교가 이뤄지고  
이를 만족하는 x만 남아서 최종 메인 쿼리 응답으로 남게된다.   

