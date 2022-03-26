1.수업소개
===

정보가 많아지면서 file만으로는 입력,저장,출력이 어려워졌다. 데이터를 정리해서 필요에 따라 잘 꺼내서 쓰고싶어하기 시작했다.  
1970년 Relational Database(관계형 데이터베이스)가 탄생한다.  
Relational Database를 이용하면 데이터를 표의형태로 정리정돈하고, 정렬과 검색 작업을 빠르고 편리하고 안전하게 할 수 있다.  
![image](https://user-images.githubusercontent.com/101965836/160125829-07d599fc-c433-4726-8731-b6afbf476455.png)  
  
MySQL이 등장하고, WEB이 정보를 저장할 데이터베이스를 찾게 되면서, 무료고 오픈소스였던 MySQL은 매우 좋은 데이터베이스 옵션이였다.   
이후 WEB과 함께 MySQL이 폭발적으로 성장하기 시작했다.  
  
  
본 수업을 통해 MySQL 데이터베이스에 대해 다뤄본다  
  
  
  ---
  ---
  
  
  
2.데이터베이스의 목적
===
스프레드시트 vs 데이터베이스 차이점에 대해 간략히 알아본 강의다.  
표의 형태로 데이터를 저장하는 공통점이 있고, 데이터베이스는 프로그래밍 언어와 버무려져서 적절히 큰 데이터를 처리하기에 적합하다는 차이가 있다.  
  
  
  
  ---
  ---
  
  
  
3.MySQL설치
===
root 비밀번호 까먹지 안도록! 주의. 까먹어도 초기화 방법이 있긴 하지만 번거롭게된다  

윈도우 환경변수에 path를 등록해야한다.  
 ![image](https://user-images.githubusercontent.com/101965836/160219186-b69880a5-d16f-4657-9bbd-3d2ad0cec676.png)  
이렇게 해줘야지  
![image](https://user-images.githubusercontent.com/101965836/160219191-9627cccb-5bc6-4b3c-b310-d0ba18600ae1.png)  
cmd 창에서 번거롭게 경로 찾아 들어가지 않아도 바로바로 mysql로 접근할 수 있게된다.  
  
  
  
  ---
  ---
  
  
  
4.MySql구조
===
결과적으론 엑셀 스프레드시트와 비슷하게 표로 저장이 된다.  
글, 댓글, 회원정보, 이메일 등등을 저장하는 표가 생긴다.  
그러면 이 나눠진 표를 잘 정리정돈 할 필요성이 생긴다  
마치 파일에서 폴더의 필요성이 생기는 것과 같은 맥락이다  
MySQL에서 연관된 표와 연관되지 않은 표를 구분하는, 파일의 폴더같은 것이 있는데 그것이 바로 Database다.  
표를 그룹핑 한게 Database? 말이 좀 헷갈린다  
  
그래서  MySql에는 스키마 라는 표현도 같이 쓴다
### 스키마
: 연관된 데이터를 담은 표들을 그룹핑 할 때 사용하는 것. 일종의 폴더 같은 것이다.  
  
스키마가 모이면 그것들이 어딘가에 저장이 되어야 한다. 그걸 **데이터베이스 서버**라고 한다.

![image](https://user-images.githubusercontent.com/101965836/160219331-7b88cbb3-6070-4e1e-94d7-15a6af712998.png)  
  
  
  
  ---
  ---
  
  
  
5.서버접속
===
데이터 베이스를 통해 얻을 수 있는 효용중 하나는 보안이다. WEB2-Nodejs 강의 막바지에 보안 문제에 간략히 다루면서 알게 되었듯, 다양한 방법을 통해서 보안 허점을 파고들어 서버의 파일을 무단으로 삭제하거나 사용자 정보가 저장된 파일을 무단으로 열람하는 등 보안 문제가 발생할 수 있다. 보안 위협들은 굉장히 방대하고 지식과 경험을 필요로 하기 때문에 개인이 어느정도 수준 이상으로 철저히 대비하기는 쉽지 않다.  
  
따라서 MySQL과 같은 데이터베이스는 데이터베이스 관리의 유용성 뿐만 아니라 보안의 측면에서도 굉장히 유용하다. 사용자별로 데이터에 접근할 권한의 차등을 줄 수 있으면서도 이를 용이하게 관리할 수 있도록 한다.  
  
  
MySQL root 계정으로 접근하기  
cmd창 관리자 권한으로 열고  
```
mysql -uroot -p
> Enter password: \**************  
```  
  
MySQL 나가기
```
> quit
bye
```
  
  
  
---
---
  
  
  
6.스키마 사용
===
## (1) 생성
```
CREATE DATABASE physicks;
```
  
## (2) 삭제
```
DROP DATABASE physicks;
```
  
## (3) 데이터베이스 목록 보기
```
SHOW DATABASES;
```
  
## (4) 데이터 베이스 선택
```
USE physicks;
```

## (5) 테이블 목록 보기
```
SHOW TABLES;
```

  
  
---
---
  
  
    
  
  
7.SQL과 테이블의 구조
===

  
# SQL
### **S** tructured  
### **Q** uery  
### **L** anguage  
S : 관계형 데이터 베이스가 표 형태로 정리한다. 그 표를 작성 해 정리하는 것을 '구조화 되었다' 라고 한다.
Q : 데이터 베이스에게 데이터를 넣고 읽고 수정하고 삭제하고 스키마 만들고 하는 것을 데이터베이스에게 요청한다는 뜻에서 쿼리라고 한다
L : 데이터 베이스에게 아무렇게 이야기 하는 게 아니라, 데이터 베이스 서버에게 SQL 언어로 요청한다.  
  
  
SQL은 
(1) 언어가 쉽다. HTML 처럼 문법 자체는 쉽다
(2) 중요하다. 표준화 까지 되어있고, 엄청 방대한 데이터를 다루는 게 데이터 베이스를 다루니까 그만큼 중요하다.

# 표
table, 표의 구조와 용어   
![image](https://user-images.githubusercontent.com/101965836/160225991-37ceb2d8-91a7-425f-9a10-6e46597a7a2e.png)  
  
  
  
---
---
  
  
    
  
8.테이블의 생성, 데이터 입출력
===

# 테이블 생성
  

>## 검색을 통해 해결하는 과정 보기
>조금 장황한 내용이지만, 단순히 코드를 따라쓰고 각 명령어나 문법을 단순 암기하기 보다는  
>상황에 따라 적절한 검색어로 구글링 하는 실력을 늘리는 게 핵심이라고 강의에서 거듭 강조한다  
>
>  
>검색어  create table in mysql cheat sheet 
>  (공식 문서를 봐도 되지만, 너무 초심자에겐 복잡할 수 있다. 
>  \~ cheat sheet를 검색어 뒤에 붙이면 잘 요약된 이미지들이 나온다  
>    
>![image](https://user-images.githubusercontent.com/101965836/160226188-2ab294a5-c8c2-4feb-986b-245fd4e242b8.png)  
>  
>```
>mysql> USE physicks;
>Database changed
>mysql> CREATE TABLE topic(...)
>```
>
>USE로 내가 편집하고자 하는 데이터 베이스로 이동,  
>CREATE TABLE topic(...)으로 테이블을 만들 수 있다.  
>그런데 위의 cheat sheet를 보면, c1 datatype(length) 같은 식으로 나와있는데 뭔지 잘 모르겠다.  
>(사실 cheat sheet는 완전 처음 배울 때 보는건 아니고, 나중에 문법 어떻게 쓰더라 같은 거 기억 안날 때 보는 용도이다)  
>그래서 다시 mysql datatype 검색.  
>
>![image](https://user-images.githubusercontent.com/101965836/160226397-9545e648-eabb-4b15-96ba-e38f059187c7.png)   
>[출처](https://www.techonthenet.com/mysql/datatypes.php)  
>  
>>## 데이터 형식은 왜?
>>엑셀과 SQL의 차이는  
>>엑셀의 셀에는 어떤 데이터나 다 넣을 수 있다. 숫자 날짜 시간 요일 문장 등등  
>>그러나 SQL에서는, 데이터가 반드시 숫자, 날짜, 알파벳 으로 들어와야 한다고 강제할 수 있다.  
>>
>>이렇게 데이터 형식을 강제하면  
>>해당 데이터들은 반드시 \[어떤] 형태이다 임을 확신할 수 있다.  
>   
>    
>이런식으로 검색을 통해서 찾아서 해결하는 힘을 길러야 한다고 강조한다  
>  
# 그래서 이번 강의에는 뭔 코드를 썼는가??


```
CREATE TABLE topic(
    -> id INT(11) NOT NULL AUTO_INCREMENT,
    -> title VARCHAR(100) NOT NULL,
    -> description TEXT NULL,
    -> created DATETIME NOT NULL,
    -> author VARCHAR(30) NULL,
    -> profile VARCHAR(100) NULL,
    -> PRIMARY KEY(id));
```
  
### id 
datatype은 INT형, 길이는 11로 정한다. 여기서 CHAR과 INT안에 들어가는 **길이**의 역할이 좀 다르다.  
> Char형의 뒤에는 캐릭터 형의 길이를 명시하기 위해 (숫자) 와 같은 형식으로 값이 붙게 되는데 Int형 뒤에도 붙일 수 있다. 
>하지만 Int형의 괄호는 숫자 개수의 제약을 의미하는 것이 아니라 Zerofill을 위한 것이다. 
>예를 들어 Int(5)는 5자리 내 숫자는 모두 0으로 채워준다는 뜻이다. 단 ORACLE에서는 실제 자리 수를 표현하는데 사용된다.
> 
>[출처](https://jins-dev.tistory.com/entry/MySQL%EC%97%90%EC%84%9C-Int%ED%98%95-%EB%92%A4%EC%9D%98-%EC%88%AB%EC%9E%90%EC%9D%98-%EC%9D%98%EB%AF%B8)  
>   
> ZEROFILL 이란 값이 123 이면 000123 처럼 앞을 0으로 채워주는 것을 말한다  


NOT NULL은 null값을 가질 수 없다는 규칙.(id 없이 데이터가 등록되면 안된다고 규정지어줌)  
AUTO_INCREMENT는 데이터가 들어올때마다 id가 1, 2, 3 이런식으로 자동으로 증가하면서 할당되도록 규정지음  
  
  
### title
VARCHAR형은 CHAR형이면서 Variable-length string을 뜻한다. 즉 변할 수 있는 글자 데이터에 varchar를 쓴다. 
길이는 100으로 해서 대충 제목 데이터니까 100글자 정도면 되니 100으로 설정한다  

### description
TEXT 형식은 char보다 더 긴 글자들을 넣기 위해서 썼다.   
![image](https://user-images.githubusercontent.com/101965836/160227447-701bd981-998b-4820-933f-5a3fcca7a56d.png)  
  
### created
생성된 날짜와 시간을 기록하기 위해 DATETIME 형식을 썼다.  
![image](https://user-images.githubusercontent.com/101965836/160227467-d7214da1-1116-4892-b9c7-cc0dfef7fd7a.png)  
  
  
### author, profile
작성자와 프로필은 비어있을 수 있으니 NULL로 두고 길이는 각 항목에 맞춰 적당히 30과 100으로 정했다.  
  
  
### PRIMARY KEY
데이터를 찾거나 정렬할때, 편의성 뿐만 아니라 속도 면에서도 어떤 기준이 되는 값이 있어야 좋다.  
여기서는 id가 1, 2, 3 , ... 으로 각각 고유한 값으로 할당되기 때문에  
PRIMARY KEY가 되기에 적합하다.  
  
  ![image](https://user-images.githubusercontent.com/101965836/160227529-9ec82123-95d9-403e-a029-90f68220f72d.png)  

  
---


9.CRUD
===

---

10.INSERT
===
데이터를 추가하는것을 CREATE라 하고 어떻게 SQL로 하는지 본다

키워드 : mySQL insert into syntax

## SHOW TABLES; 
 
```
mysql\> SHOW TABLES;
+--------------------+
| Tables_in_physicks |
+--------------------+
| topic              |
+--------------------+
1 row in set (0.01 sec)
```
현재 데이터베이스의 테이블들을 보여준다. 


## DESC topic;
```
mysql> DESC topic;
+-------------+--------------+------+-----+---------+----------------+
| Field       | Type         | Null | Key | Default | Extra          |
+-------------+--------------+------+-----+---------+----------------+
| id          | int          | NO   | PRI | NULL    | auto_increment |
| title       | varchar(100) | NO   |     | NULL    |                |
| description | text         | YES  |     | NULL    |                |
| created     | datetime     | NO   |     | NULL    |                |
| author      | varchar(30)  | YES  |     | NULL    |                |
| profile     | varchar(100) | YES  |     | NULL    |                |
+-------------+--------------+------+-----+---------+----------------+
6 rows in set (0.01 sec)
```
DESC [테이블이름]; 하면 해당 테이블의 양식을 보여준다

## INSERT INTO topic (...) VALUES(...)
```
mysql> INSERT INTO topic (title,description,created,author,profile) 
  -> VALUES('MySQL','MySQL is ...',NOW(),'egoing','developer');
Query OK, 1 row affected (0.01 sec)
```
INSERT INTO topic (입력할 데이터의 Field) VALUES(입력할 데이터)  
위의 DESC [테이블이름]; 명령어를 참고해서 적으면 된다.  
위 코드 예제에서 id는 자동으로 할당되도록 했으므로 입력해 넣지 않아도 된다.  
  
  
  
---
  
  
11.SELECT
===

## SELECT * FROM topic
![image](https://user-images.githubusercontent.com/101965836/160229354-c951acdb-68f1-42a5-a5f7-5c8fb9539a28.png)  
모든 데이터를 출력한다.  
  
  
## SELECT [표시할 값] FROM topic
![image](https://user-images.githubusercontent.com/101965836/160229406-38a2e644-af52-4246-93aa-9174cadfbda7.png)   
  
id title created author만 표시하도록 했다.  
  
  
이외에 내림차순으로 정렬할 수도 있고
WHERE author='egoing'를 뒤에 추가해서 author이 egoing인 데이터만 출력하는 등이 가능하다.  
  
다양한 기능이 있으니 검색을 통해 적절히 알아내면 된다.  
  
  
---
  
  
  
12.UPDATE
===
![image](https://user-images.githubusercontent.com/101965836/160229582-d02ec4ec-48f2-4c3e-93b8-b2e9f761a4ac.png)  
UPDATE 테이블이름 SET column이름='값'

### 수정 전 데이터
![image](https://user-images.githubusercontent.com/101965836/160229636-2b640f44-c96e-4960-ab5d-96aa3567d4f0.png)    
ORACLE is 뒤에 ...을 update 하고 싶다!  

### 수정 명령
![image](https://user-images.githubusercontent.com/101965836/160229694-7de79fc1-e48a-4b55-b237-00705587e530.png)  
   
UPDATE topic SET description='Oracle is ...', title='Oracle' WHERE id=2;  
**뒤에 WHERE 안 붙이면 전부 다 Oracle is... 으로 바뀐다!**
  
  
### 수정 후 데이터
![image](https://user-images.githubusercontent.com/101965836/160229676-7490516f-eeef-43c6-bb43-e7241d5b284e.png)  
  
---

13.DELETE
===
![image](https://user-images.githubusercontent.com/101965836/160229730-e4bfbf30-0442-4410-abe3-c6e39a48d7d2.png)  
  
  
DELETE FROM topic WHERE id = 5;  
where 안쓰면 전부 삭제되니 주의  
  
  
  
---

14.수업의정상
===

---

15.관계형데이터베이스의 필요성
===

![image](https://user-images.githubusercontent.com/101965836/160236857-8de2861f-bd80-4704-b409-2876207e75cd.png)  
반복적으로 등장하는 데이터들을 볼 수 있다.  
  
  
이 때, 번거로워질 많은 시나리오가 그려진다.  
egoing으로 중복되는 데이터가 같은 사람이 아니라 동명이인일수도 있다. 이를 구분하기 위한 연산이 이뤄져야 할 수 있다.    
또 중복 되는 데이터들에 대해 연산해야 할 수 있다. 예를 들어 developer를 computer scientist로 일괄 변경해야 할 수 있다.   
또 developer들의 이름만 가져와야 할 수 있다.  
  
  
이처럼 다양한 시나리오에서 다양한 반복작업이 이뤄질 수 있다.  
  
## 더 자세한 예시
예를 들어 아래 이미지와 같이 개편이 이뤄져야 할 수 있다.  
  
### 변경 전
![image](https://user-images.githubusercontent.com/101965836/160237018-696776bf-4cff-4fcf-831c-66261d3fd1b5.png)  
  
### 변경 후
![image](https://user-images.githubusercontent.com/101965836/160237354-05501986-829f-4059-a3de-4fc13bfcf255.png)  

  
**사람(author)** 에 대한 정보와  
**글(topic)** 에 대한 정보를 분리시켰다  
  
이렇게 바뀌면 어떤 장단점이 있을까?  
#### 장점 
author에 대한 정보를 변경하려면, 단순히 author 테이블의 해당 사람 정보만 바꾸면 된다.  
예를 들어 **egoing** 을 **이고잉** 으로 바꿔야 한다고 해보자.  
기존에는 모든 **egoing** 을 찾아서 바꿔야 했었지만,  
바뀐 테이블에서는 그냥 **author** 에서 **egoing** 하나만 이고잉으로 바꾸면 된다.  
이렇게 적절히 테이블을 분리함으로써 많은 작업이 간략화됨을 알 수 있다!  
  
#### 단점
기존에는 하나의 표에 다 드러나기 때문에 곧장 이해할 수 있다.  
그러나 바뀐 테이블은 author의 참조값인 author_id를 표시하므로 직관성이 떨어진다.    


## 하지만 MySQL을 활용해 단점을 극복할 수 있다

![image](https://user-images.githubusercontent.com/101965836/160237360-56317e2d-6a79-47c1-b00f-07cc639e5a1e.png)  
이렇게 테이블들이 author와 topic으로 각각 따로따로 있지만,  
  
![image](https://user-images.githubusercontent.com/101965836/160237382-f3218bf3-1ad2-4d84-893f-c84f95bfcaa4.png)
이렇게 MySQL을 통해서 하나로 볼 수 있다.  
  

---
\
\
\
16.테이블 분리하기
===
### 기존 테이블 백업(rename)
![image](https://user-images.githubusercontent.com/101965836/160238169-cf34c753-b7a3-42fd-b152-239f93fac5c4.png)  
  
  
  
  
### 테이블 만들기
![image](https://user-images.githubusercontent.com/101965836/160237815-9283dc06-08ed-4c68-94a4-e96bae0c4e87.png)  
```
CREATE TABLE `author` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(20) NOT NULL,
  `profile` varchar(200) DEFAULT NULL,
  PRIMARY KEY (`id`)
);
```
```
CREATE TABLE `topic` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `title` varchar(30) NOT NULL,
  `description` text,
  `created` datetime NOT NULL,
  `author_id` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
);
```

### 테이블에 데이터 넣기
![image](https://user-images.githubusercontent.com/101965836/160237883-f1790be5-b539-4b03-ac3e-4fa28d264e49.png)  
```
INSERT INTO `author` VALUES (1,'egoing','developer');
INSERT INTO `author` VALUES (2,'duru','database administrator');
INSERT INTO `author` VALUES (3,'taeho','data scientist, developer');
```
```
INSERT INTO `topic` VALUES (1,'MySQL','MySQL is...','2018-01-01 12:10:11',1);
INSERT INTO `topic` VALUES (2,'Oracle','Oracle is ...','2018-01-03 13:01:10',1);
INSERT INTO `topic` VALUES (3,'SQL Server','SQL Server is ...','2018-01-20 11:01:10',2);
INSERT INTO `topic` VALUES (4,'PostgreSQL','PostgreSQL is ...','2018-01-23 01:03:03',3);
INSERT INTO `topic` VALUES (5,'MongoDB','MongoDB is ...','2018-01-30 12:31:03',1);
```


17.JOIN
===
서로 독립적인 테이블을 읽을 때, 마치 하나의 테이블 처럼 표시하는 기능이다  
  
  
![image](https://user-images.githubusercontent.com/101965836/160238265-eaecbac8-0937-4e06-956a-c623c70fdc6e.png)  
  
topic 테이블과 author 테이블을 따로따로 보면  
topic 테이블의 author_id가 어떤 사람을 뜻하는지 쉽게 알 수 없다.  
따라서 join 기능을 이용해서 topic 테이블의 author_id와 author 테이블의 id를 매칭 시켜서 볼 수 있다.  
  
  
# JOIN 사용법

## (1) 같이 띄워서 보기
![image](https://user-images.githubusercontent.com/101965836/160238367-c236e5c8-dfbf-47d2-95e4-9ee634bbefb9.png)  
  
  
```
SELECT * FROM topic LEFT JOIN author ON topic.author_id = author.id;
```

단순히  
SELECT * FROM topic LEFT JOIN author; 라고 해버리면
어떤 기준으로 두 테이블을 연관지어야 할 지 알 수 없다.  
  
  
따라서 뒤에
SELECT * FROM topic LEFT JOIN author ***ON topic.author_id = author.id*** ;  
ON 하고 어떤 값끼리 매칭시켜줘야 할지 알려줘야 한다.  



## (2) 원하는 항목들만 띄워서 보기

![image](https://user-images.githubusercontent.com/101965836/160238502-d6d3f35c-5851-4d55-9fb8-b7d5deca3734.png)  
  
```
SELECT topic.id,title,description,created,name,profile FROM topic LEFT JOIN author ON topic.author_id = author.id;
```

<ol> 주의할 점 </ol>
SELECT **topic.id** , ...

만약에 이걸 그냥  
SELECT **id** , ... 으로 적어버리면

![image](https://user-images.githubusercontent.com/101965836/160238865-ffad8a38-c2ae-4329-ba3a-e4ba1ead0d16.png)  
  
이렇게 **ambiguous** 하다는 에러를 띄운다. 즉, **id** 가 topic의 id인지 author의 id 인지 모호하다는 뜻이다.  
  
  
## (3) 원하는 항목의 이름을 바꿔서 띄우기

![image](https://user-images.githubusercontent.com/101965836/160238992-4197961c-44e2-408a-82fc-d79c1158c3a8.png)  
  
```
mysql> SELECT topic.id AS topic_id,title,description,created,name,profile FROM topic LEFT JOIN author ON topic.author_id = author.id;
```
SELECT **topic.id** , ... 이였던 부분을  
SELECT **topic.id AS topic_id** , ... 으로 바꿨다  
  
  
### 이게 어떻게 유용하게 응용될 수 있는가?
  
관계형 데이터 베이스의 강점이 여기서 드러난다.  
![image](https://user-images.githubusercontent.com/101965836/160239307-3db6a926-1d9d-4eda-8396-b6bf9b949d6f.png)   
이렇게 comment 테이블이 새로 생겼다 하더라도 author_id 값만 있으면 author 테이블과 연관지을 수 있다.  
   
   
![image](https://user-images.githubusercontent.com/101965836/160239351-eeac4151-27ff-4bc5-83c1-154d9cfad7fd.png)  
![image](https://user-images.githubusercontent.com/101965836/160239450-a1d2451f-f7ef-4da9-82b0-06b0a11cc981.png)  
  
  이처럼 JOIN을 활용하고, 또 원하는 내용만 띄우는 방식 등으로 유용하게 활용할 수 있다.  
  
  
![image](https://user-images.githubusercontent.com/101965836/160239440-e3a306da-c6ce-49f7-bfb1-a8a96dc88b0c.png)
![image](https://user-images.githubusercontent.com/101965836/160239459-fa50b45e-95ed-41f7-b090-d4b04fa41dd3.png)


  이 뿐만 아니라, Update에도 곧바로 바뀌어서 대응할 수 있으니, 이런 측면에서 매우 편하다.  
  
  
---
  
  
  
18.인터넷과 데이터베이스의 관계
===
인터넷과 데이터베이스가 만나서 폭발적인 효과를 낸다.  


