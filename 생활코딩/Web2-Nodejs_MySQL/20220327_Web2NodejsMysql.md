1.수업소개
===
Web2 Nodejs에서 만든 App과, 생성된 글을 읽고 수정할 수 있다
![image](https://user-images.githubusercontent.com/101965836/160280897-18c2e6a3-5466-4b49-b9ae-c36b60c323e3.png)  
그리고 이 Web2 Nodejs에서 만든 데이터는, data 디렉토리에 저장됐었다.  
웹 페이지는 data 디렉토리의 파일을 main.js가 감지해서 표현해 주었다.  
  
  
파일은 설치할 필요없이, 아주 단순하게, 어느 컴퓨터에서나 사용할 수 있다.  
하지만 단점이 있다.  

# 기존 방식의 단점
우리가 가진 페이지가 1억개 라고 생각해보자.  
1억개의 파일이 저장되어있고, 1억개의 파일이 별도의 정보를 갖고 있으며, 이를 수시로 검색하는 요청이 오고간다면.  
이는 파일 형태로 관리하기에는 굉장히 부담된다  
또한 경우에 따라서 특정 항목을 기준으로 정렬된 데이터가 필요할 수 있다. 예를 들어 저자가 egoing 인 데이터만 갖고 와야 할 때, 파일 형태로 저장되어 있을 경우 이 작업이 힘들다.  

# MySQL 데이터 베이스로 다룬다
main.js 코드에는 파일을 핸들링 하고 있는 fs.readdir fs.readFile 같은 부분이 있다.  
이제 이 코드들을 nodejs와 mysql을 연관지어주는 코드를 가져와서 main.js의 코드를 변경할 것이다.   
이렇게 하여서 mysql의 보안 안정성 성능 동시성 등 여러가지 이점을 가져갈 수 있다.  

---

2.실습준비
===
[실습소스코드](https://github.com/web-n/node.js-mysql/releases/tag/1)  
zip 다운해서 풀면 된다.  
  
DATABASE2-MySQL에서 실습한 데이터베이스를 사용 할 것인데, 해당 실습때 썼던 데이터베이스가 있으면 그대로 쓰면 된다. 만약 없다 하더라도, 위의 실습 코드의 메인이 되는 폴더에 example.sql 파일에 해당 데이터가 있다.
  
  
```
npm install
```

또한 터미널을 실행한 다음 main 폴더로 이동해 npm install을 입력하면 package.json 파일을 자동으로 인식해서 디펜던시 인식해서 패키지들을 자동으로 설치한다. 설치가 끝나면 node_modules가 생기고 설치된 모듈들을 볼 수 있다.  
  
# NPM이란?
Node Package Manager(혹은 Node Package Module)의 줄임말로써 Python의 pip나 Ruby의 gem처럼 Node.js의 패키지를 관리할 수 있는 도구이다. 또한 npm을 사용하여 패키지를 공유하는 온라인 패키지 저장소의 이름이기도 하다.   


---

3.Mysql설치
===
# nodejs를 위한 npm MySQL모듈 설치
패키지 설치법 소개 페이지 [npm mysql](https://www.npmjs.com/package/mysql)<br>
  
vscode에서 터미널창 열고,  
```
npm install --save mysql
```
여기서 --save 또는 -S 명령어는  
설치하고자는 mysql 모듈을 package.json의 dependency에 추가하는 것 까지 같이 하는 것이다.  
  
### 설치 전 
![image](https://user-images.githubusercontent.com/101965836/160311175-3571a554-19ba-462a-85ca-0422c3842d4c.png)  
### 설치 후
![image](https://user-images.githubusercontent.com/101965836/160311238-e8de5289-b782-4f69-9e45-0e00183efa90.png)  
```
"dependencies": {
    "mysql": "^2.18.1",
    "sanitize-html": "^1.18.2"
  }
```
이와 같이 mysql이 디펜던시로 추가된 것을 볼 수 있다.  
  
> 여기서 밑에 뜨는 3 vulnerabilities ( ... )과 npm audit fix 명령들은 보안 취약점 관련 메세지이다.
> "현재 너의 패키지중에 \~\~\~ 같은 보안 취약점이 있다" 라고 알려주는 것이다.
> npm audit을 하면 상세하게 어떤 패키지에 보안 취약점이 있는지 알려주고
> npm audit fix하면 이를 해결하기 위한 작업이 진행된다.(최신 버전으로 업데이트)
> 일단 현재 강의 진행에는 크게 상관없으니, 해당 메세지는 무시하기로 했다. 
  
# 예제 코드
설치가 완료된 후 예제 코드를 살펴 본다.   
```JavaScript
var mysql      = require('mysql');
var connection = mysql.createConnection({
  host     : 'localhost',
  user     : 'me',
  password : 'secret',
  database : 'my_db'
});
 
connection.connect();
 
connection.query('SELECT 1 + 1 AS solution', function (error, results, fields) {
  if (error) throw error;
  console.log('The solution is: ', results[0].solution);
});
 
connection.end();
```

(1) mysql 모듈을 사용하며, mysql이라는 변수를 통해 사용할 수 있다.
(2) mysql.createConnextion(...) 모듈안의 createConnextion매소드를 사용하고, 인자로 {...} 안의 객체를 주도록 약속했나보다. 
(3) 인자로 넘기는 객체를 보자. host는 서버가 어떤 컴퓨터에 있는가를 뜻한다. nodejs와 mysql 서버가 같은 컴퓨터에 있다면, 같은 컴퓨터에 있다는 localhost로 하면 된다. 
이후 설정들은 본인이 mysql에서 사용자 명과 비밀번호를 어떻게 했고, 데이터베이스 이름은 뭐로 해서 만들었는지 따라 다르다.
```
  user     : 'root',
  password : '111111',
  database : 'opentutorials'
```
강의에서는 위와 같이 수정 했다.  
