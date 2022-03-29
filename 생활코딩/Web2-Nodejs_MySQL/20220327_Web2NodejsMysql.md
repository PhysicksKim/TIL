1.수업소개
===
이전에 Web2 Nodejs 수업으로 배우고 만든 결과물을 보자.
![image](https://user-images.githubusercontent.com/101965836/160280897-18c2e6a3-5466-4b49-b9ae-c36b60c323e3.png)  
nodejs를 통해 웹app을 만들었고, 웹에서 사용자가 글을 생성하고 읽고 수정하고 삭제하는 것(CRUD)을 구현할 수 있었다.
  
그런데 이 Web2 Nodejs에서 만든 데이터는 data 디렉토리에 저장됐었다.  
웹 페이지는 data 디렉토리의 파일을 main.js가 감지해서 표현해 주는 방식이였다.  

# nodejs & mysql 수업을 배우면
이전에 서버에 파일 형식으로 저장했던 데이터들을 MySQL에서 관리하도록 배우게 된다.  
데이터베이스(MySQL)를 사용하는이유는 파일로 데이터를 저장할때의 단점을 보완하기 위해서이다.
  
# 기존 방식의 단점  
  
일단 기존 방식이던 파일로 서버의 디렉토리를 바로 이용하면 어떨까?
파일은 설치할 필요없이, 아주 단순하게, 어느 컴퓨터에서나 사용할 수 있다.  
하지만 단점이 있다.  
  
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
  
  
(4) connection이라는 변수에 그 결과가 담긴다
(5) connection.connect();  
connection 객체에 .connect()라는 매소드를 호출하면 접속이 될 것이다 라는 것을 알려준다.  

(6) connection.query('SQL문', 콜백함수(...){...}); <br>
- 6-1. SQL문은 말 그대로 SQL 코드를 입력해서 값을 요청하는 부분이다. 그리고 입력된 SQL문에 따라서 콜백 함수의 인자로 값을 반환한다.  
- 6-2. 콜백함수는 function (error, results, fields) {...}로 구성된다. 에러가 나면 error에 메세지가 담겨서 반환될테고, 값은 results에 넣어져서 반환될것이다. 
해당 메소드와 관련된 자세한 내용은 [공식문서링크](https://www.npmjs.com/package/mysql#performing-queries) 를 참조.  

## 코드 수정 후
```JavaScript
var mysql      = require('mysql');
var connection = mysql.createConnection({
  host     : 'localhost',
  user     : 'root',
  password : '(your mysql root password)',
  database : '(your database name)'
});
 
connection.connect();
 
connection.query('SELECT * FROM topci', function (error, results, fields) {
  if (error) {
      console.log(error)
  }
  console.log(results);
});
 
connection.end();
```

# Trouble Shooting 

위의 수정 후 코드로 실행 했는데 에러가 났다.  
  
![image](https://user-images.githubusercontent.com/101965836/160362649-66446260-0e22-43b7-8dee-357d450f8ac2.png)  
  
```
KHC@DESKTOP-TNMP2Q4 MINGW64 /g/MyHistory/Learning/node.js-mysql-1/node.js-mysql-1
$ node nodejs/mysql.js 
Error: ER_NOT_SUPPORTED_AUTH_MODE: Client does not support authentication protocol requested by server; 
  ...
  ... 
```  

<br>
<br>  
  
# 에러 ER NOT SUPPORTED AUTH MODE
[참고한 글](https://stackoverflow.com/questions/50093144/mysql-8-0-client-does-not-support-authentication-protocol-requested-by-server)  

## 원인 
MySQL 8.0버전으로 업그레이드 되면서, 디폴트인 인증 방식(PAM; pluggable authentication methods)이 **mysql_native_password** 에서 **Caching SHA-2 Pluggable Authentication** 방식으로 바뀌었다고 한다. 그런데 nodejs의 mysql패키지는 이를 아직 지원하지 않아서 에러가 발생한 것이다.


## 해결 방법  
  
<br> 
크게 2가지 방법이 있다. <br>
<br>   
  
#### 추천 하는 방법   
(1) npm으로 mysql2 를 설치 
앞서 설명했듯 node의 mysql패키지가 바뀐 인증 방식을 지원하지 않는다. 따라서 최신 업데이트가 이뤄진 mysql2를 사용하면 된다. [mysql2](https://www.npmjs.com/package/mysql2)  
  
먼저 터미널을 열고 아래를 입력한다. 
```
npm un mysql && npm i mysql2
```
이후 코드에 있는 require('mysql') 을 전부 require('mysql2')로 바꾸면 된다.

#### 추천하지 않음 
(2) mysql installer > server 옆에 reconfigure > Auth 설정가서 legacy 선택
![image](https://user-images.githubusercontent.com/101965836/160325365-e63afbec-3eaa-41fe-845d-184895e5c372.png)  
![image](https://user-images.githubusercontent.com/101965836/160325422-c6b176c0-8ed3-4b91-bcc8-4490565c09da.png)  
   
   
 
자 이제, 저 **쿼리문** 을 개발자가 적절히 잘 짜서 사용한다면, 이제 상황에 따라 알맞게 데이터를 잘 가져올 수 있는 것이다!!  
  
  
# 추가
비밀번호가 main코드 안에 있는게 싫어서, 따로 파일을 만들어 로컬에 저장해두고 불러오는 식으로 했다.
```
mypassword = fs.readFileSync('mypassword','utf8');
var db = mysql.createConnection({
  host:'localhost',
  user:'root',
  password: mypassword,
  database:'physicks'
});
```
물론 이렇게 하면 외부에서 파일에 접근해 비밀번호를 알아낼 수 있으므로, 그냥 코드에 적어넣는 것보다 더 위험하다고 생각한다. 다만 포스트를 작성하는 과정에 copy&paste 하다가 괜히 비밀번호가 노출되는 것을 방지하기 위해 이렇게 했다.  
  
---

4.MySQL로 메인페이지 구현 (Read)
===

더이상 웹 페이지가 data 폴더에서 데이터를 가져오지 않고 mysql에서 데이터를 가져오도록 해보자.  
  
```JavaScript
if(pathname === '/'){
    if(queryData.id === undefined){
        fs.readdir('./data', function(error, filelist){
        var title = 'Welcome';
        var description = 'Hello, Node.js';
        var list = template.list(filelist);
        var html = template.HTML(title, list,
          `<h2>${title}</h2>${description}`,
          `<a href="/create">create</a>`
        );
        response.writeHead(200);
        response.end(html);
      });
    } else { ...
```
pathname === '/' 는 최상위 경로라는 뜻이다. 즉, 여기가 첫 메인페이지를 띄우는 코드 부분이다. fs.readdir부터 파일을 읽고 정보들을 가져와서 html문서로 만들고 html 변수에 저장해서 response.end(html)로 보내는 것을 알 수 있다.  
  
  
이 부분을  
```JavaScript
if(queryData.id === undefined){
  db.query(`SELECT * FROM topic`, function(err, results, fields){
    console.log(results);
    response.writeHead(200);
    response.end('Success');
  });
}
```  
이렇게 바꿨다

    
이렇게 하고 페이지에 다시 접속해보면 Success라는 글자가 뜨고, 터미널에는 results 값으로 데이터베이스에 저장된 데이터들이 주루룩 뜬다.
  
  
# 메인 페이지 수정
```JavaScript
if(queryData.id === undefined){
  db.query(`SELECT * FROM topic`, function(err, results, fields){
    var title = 'Welcome';
    var description = 'Hello, Node.js';
    var list = template.list(results);
    var html = template.HTML(title, list, 
      `<h2>${title}</h2>${description}`,
      `<a href="/create">create</a>`
    );

    response.writeHead(200);
    response.end(html);
  });
} else { ...
```
이전에 있던 코드와 거의 동일하고, var list = template.list(results); 부분만 results로 바꿨다. results에 Mysql에서 가져온 데이터가 담겨있기 때문이다. 그다음, template.list() 매소드가 제대로 작동하는지 봐야 하는데, 일단 그대로 실행시키면 아래와 같이 뜬다.  
![image](https://user-images.githubusercontent.com/101965836/160371601-c5f9271d-9f63-44ee-af9b-6ab0c2c9775d.png)
<br>
<br>
이유는 lib/template.js에서 list 메소드를 보면 알 수 있다. 
```JavaScript
list:function(topics){
    var list = '<ul>';
    var i = 0;
    while(i < topics.length){
      list = list + `<li><a href="/?id=${topics[i]}">${topics[i]}</a></li>`;
      i = i + 1;
    }
    list = list+'</ul>';
    return list;
  }
}
```
보면 \<a ... \> \</a\> 부분에서 그냥 topics[i]라고 되어 있는데, 이렇게하면 mysql에서 전달해준 results의 각각 원소들은 Object를 담고 있기 때문에 object라고 뜨는 것이다. 따라서 이 부분을 수정해보자

## template.js의 list 매소드 수정
```
<li><a href="/?id=${topics[i].id}">${topics[i].title}</a></li>
```
쿼리 스트링이 될 부분은 고유한 값인 id로 해줬고, 사용자가 클릭하게 될 텍스트는 title로 해줬다.

![image](https://user-images.githubusercontent.com/101965836/160372184-fe923cbf-85d4-47ae-9843-141406a1374a.png) ![image](https://user-images.githubusercontent.com/101965836/160372213-72220991-0015-46ad-bf69-a34e20046361.png)  
<br> 
이렇게 목록 이름도 제대로 나오고, id값으로 주소가 잘 들어가는 것을 볼 수 있다.  
  
  
---
  
5.MySQL로 상세보기 페이지 구현 (Read)
===
```JavaScript
else {
   fs.readdir('./data', function(error, filelist){
    var filteredId = path.parse(queryData.id).base;
    fs.readFile(`data/${filteredId}`, 'utf8', function(err, description){
      var title = queryData.id;
      var sanitizedTitle = sanitizeHtml(title);
      var sanitizedDescription = sanitizeHtml(description, {
        allowedTags:['h1']
      });
      var list = template.list(filelist);
      var html = template.HTML(sanitizedTitle, list,
        `<h2>${sanitizedTitle}</h2>${sanitizedDescription}`,
        ` <a href="/create">create</a>
          <a href="/update?id=${sanitizedTitle}">update</a>
          <form action="delete_process" method="post">
            <input type="hidden" name="id" value="${sanitizedTitle}">
            <input type="submit" value="delete">
          </form>`
      );
      response.writeHead(200);
      response.end(html);
    });
  }); 
}
```
이부분이 상세보기 페이지를 출력하는 부분이다.  
일단 앞서서 메인 페이지 출력하던 부분에 썼던 코드를 재활용 해보자.  

<br>
<br> 
이제 뭘 짜야할까? \ht\tp://localhost:3000/?id=1 이처럼 끝에 id=1이 붙으니, id 값에 따라서 테이블에서 행을 가져오면 된다.  
<br> 
  
## id 값에 따라서 테이블 가져오기  
  
```  
mysql> SELECT * FROM topic WHERE id = 3;
+----+------------+-------------------+---------------------+-----------+
| id | title      | description       | created             | author_id |
+----+------------+-------------------+---------------------+-----------+
|  3 | SQL Server | SQL Server is ... | 2018-01-20 11:01:10 |         2 |
+----+------------+-------------------+---------------------+-----------+
1 row in set (0.00 sec)
```  
<br>
<br>  
  
#### 먼저 throw err을 추가했다.  
  
```Javascript  
db.query(`SELECT * FROM topic`, function(err, results){
  if(err){
    throw err;
  }
  db.query(`SELECT * FROM topic WHERE id = ${queryData.id}`, 
    function(err2, result){
      if(err2){
        throw err2;
      }
      ...(이후 본문 띄우는 코드)...
    });
  });
```  
  
throw error; 를 사용하면, 에러 발생 시 에러 메세지를 console창에 띄우고 app을 즉시 중단한다.  
  
  
#### 그 다음 result에 들어있는 title같은 값들을 변수에 넣어준다
```Javascript
  ...
  var title = result[0].title;
  var description = result[0].description;
  var list = template.list(results);
  ...
```

이렇게 되면 페이지들이 이제 내용까지 포함해서 출력하게 된다. <br> 

![image](https://user-images.githubusercontent.com/101965836/160375788-2f57e637-3081-4680-9751-1258b7e3b3d7.png)

# 주의할 점
```Javascript
db.query(`SELECT * FROM topic WHERE id = ${queryData.id}` , ...
```
이렇게 id값을 주게 되면, 데이터 베이스가 갖고 있는 코드의 특성에 의해 공격을 당할 수 있다.    
  
따라서 아래와 같이 바꾸는게 좋다
``` 
db.query(`SELECT * FROM topic WHERE id =?`,[queryData.id], ...
```
이렇게 하면 ?의 값은 뒤에 나오는 배열 값이 들어가도록 자동적으로 처리가 되며, 만약 공격이 들어오더라도 mysql에서 알아서 걸러주는 기능이 동작하게 된다.   

## 추가 수정
```Javascript
var html = template.HTML(title, list, 
  `<h2>${title}</h2>${description}`,
  `<a href="/create">create</a>
    <a href="/update?id=${queryData.id}">update</a>
    <form action="delete_process" method="post">
    <input type="hidden" name="id" value="${queryData.id}">
    <input type="submit" value="delete">
  </form>`
);
```
update 버튼이랑 delete 버튼을 그대로 살려두기 위해 코드를 추가했다.

<br> 
<br> 
<details>
  <summary>전체 코드</summary>
        
        
      var http = require('http');
      var fs = require('fs');
      var url = require('url');
      var qs = require('querystring');
      var template = require('./lib/template.js');
      var path = require('path');
      var sanitizeHtml = require('sanitize-html');
      var mysql = require('mysql2');

      mypassword = fs.readFileSync('mypassword','utf8');
      var db = mysql.createConnection({
        host:'localhost',
        user:'root',
        password: mypassword,
        database:'physicks'
      });
      db.connect();

      var app = http.createServer(function(request,response){
          var _url = request.url;
          var queryData = url.parse(_url, true).query;
          var pathname = url.parse(_url, true).pathname;
          if(pathname === '/'){
            if(queryData.id === undefined){
              db.query(`SELECT * FROM topic`, function(err, results, fields){
                var title = 'Welcome';
                var description = 'Hello, Node.js';
                var list = template.list(results);
                var html = template.HTML(title, list, 
                  `<h2>${title}</h2>${description}`,
                  `<a href="/create">create</a>`
                );
                response.writeHead(200);
                response.end(html);
              });
            } else {
              db.query(`SELECT * FROM topic`, function(err, results){
                if(err){
                  throw err;
                }
                db.query(`SELECT * FROM topic WHERE id =?`,[queryData.id], function(err2, result){
                  if(err2){
                    throw err2;
                  }
                  var title = result[0].title;
                  var description = result[0].description;
                  var list = template.list(results);
                  var html = template.HTML(title, list, 
                    `<h2>${title}</h2>${description}`,
                    `<a href="/create">create</a>
                      <a href="/update?id=${queryData.id}">update</a>
                      <form action="delete_process" method="post">
                      <input type="hidden" name="id" value="${queryData.id}">
                      <input type="submit" value="delete">
                    </form>`
                  );
                  response.writeHead(200);
                  response.end(html);
                });
              });
            }
          } else if(pathname === '/create'){
            fs.readdir('./data', function(error, filelist){
              var title = 'WEB - create';
              var list = template.list(filelist);
              var html = template.HTML(title, list, `
                <form action="/create_process" method="post">
                  <p><input type="text" name="title" placeholder="title"></p>
                  <p>
                    <textarea name="description" placeholder="description"></textarea>
                  </p>
                  <p>
                    <input type="submit">
                  </p>
                </form>
              `, '');
              response.writeHead(200);
              response.end(html);
            });
          } else if(pathname === '/create_process'){
            var body = '';
            request.on('data', function(data){
                body = body + data;
            });
            request.on('end', function(){
                var post = qs.parse(body);
                var title = post.title;
                var description = post.description;
                fs.writeFile(`data/${title}`, description, 'utf8', function(err){
                  response.writeHead(302, {Location: `/?id=${title}`});
                  response.end();
                })
            });
          } else if(pathname === '/update'){
            fs.readdir('./data', function(error, filelist){
              var filteredId = path.parse(queryData.id).base;
              fs.readFile(`data/${filteredId}`, 'utf8', function(err, description){
                var title = queryData.id;
                var list = template.list(filelist);
                var html = template.HTML(title, list,
                  `
                  <form action="/update_process" method="post">
                    <input type="hidden" name="id" value="${title}">
                    <p><input type="text" name="title" placeholder="title" value="${title}"></p>
                    <p>
                      <textarea name="description" placeholder="description">${description}</textarea>
                    </p>
                    <p>
                      <input type="submit">
                    </p>
                  </form>
                  `,
                  `<a href="/create">create</a> <a href="/update?id=${title}">update</a>`
                );
                response.writeHead(200);
                response.end(html);
              });
            });
          } else if(pathname === '/update_process'){
            var body = '';
            request.on('data', function(data){
                body = body + data;
            });
            request.on('end', function(){
                var post = qs.parse(body);
                var id = post.id;
                var title = post.title;
                var description = post.description;
                fs.rename(`data/${id}`, `data/${title}`, function(error){
                  fs.writeFile(`data/${title}`, description, 'utf8', function(err){
                    response.writeHead(302, {Location: `/?id=${title}`});
                    response.end();
                  })
                });
            });
          } else if(pathname === '/delete_process'){
            var body = '';
            request.on('data', function(data){
                body = body + data;
            });
            request.on('end', function(){
                var post = qs.parse(body);
                var id = post.id;
                var filteredId = path.parse(id).base;
                fs.unlink(`data/${filteredId}`, function(error){
                  response.writeHead(302, {Location: `/`});
                  response.end();
                })
            });
          } else {
            response.writeHead(404);
            response.end('Not found');
          }
      });
      app.listen(3000);

       
</details>


---

6.Mysql로 글생성 기능 구현 (Create)
===
Create 기능 구현  

현재 구현된 Create의 동작 방식은 
### (1) else if(pathname === '/create'){ ... }   
create할 내용 입력하는 페이지. post방식으로 데이터 전달, submit 누르면 /create_proces로 페이지 이동.  
### (2) else if(pathname === '/create_process'){ ... }
request.on('data', function(data){...} 으로 'data'가 차곡차곡 수신되면 변수에다가 차곡차곡 저장함.  
request.on('end', function(){...} 으로 'end'로 수신이 끝나면, 안의 callback 함수 실행.  
  
따라서 /create_process 의 request.on('end', function(){...} 부분에다가 sql문을 전송해주는 코드를 작성하면 된다.  
  
# 데이터 추가하는 SQL문
```
INSERT INTO topic (title, description, created, author_id) VALUES('Nodjs', 'Nodejs is...', NOW(), 1);
```
일단 author_id는 나중에 구현하도록 하고, 위와 같이 INSERT INTO ...을 통해서 값을 넣을 수 있다.

# 코드로 구현
```JavaScript
request.on('end', function(){
  var post = qs.parse(body);
  db.query(`
    INSERT INTO topic (title, description, created, author_id) 
      VALUES(?, ?, NOW(), ?)`,
      [post.title, post.description, 1],
      function(error,result){
        if(error){
          throw error;
        }
        response.writeHead(302, {Location: `/?id=${result.insertId}`});
        response.end();
      }
    )
});
```
## (1)  db.query(\`SQL코드\`,\[...],function(...)\{...});
.query 매소드를 통해서 SQL코드를 전송하고 콜백 함수에 따라서 동작하도록 할 수 있다.

## (2) \`SQL코드\`,\[...]
```
`INSERT INTO topic (title, description, created, author_id) VALUES(?, ?, NOW(), ?)`
```
여기서 물음표(?) 부분은 뒤에 나오는 \[...]의 원소가 순서대로 대입된다. ? 3개는 각각 타이틀, 디스크립션, author_id 값이 들어가야 한다. 따라서 \[post.title, post.description, author_id]로 처리되어야 하는데, author_id는 조금 구현과정이 필요하므로 일단 1로 적어두었다.

## (3) result.inserId
SQL에 데이터를 삽입하기 때문에 어떤 result가 들어올지 감이 안잡힐 수 있다. 여기서 콜백함수의 result는 데이터를 삽입한 뒤에 필요한 정보들이 나온다. 이 강의 코드의 경우, MySQL설정에 따라 id는 데이터가 삽입된 순서대로 증가하면서 배정된다. 그리고 result는 자동으로 할당된 id를 반환하는 부분이 있는데 그 매소드가 바로 .insertID이다.   
```JavaScript
response.writeHead(302, {Location: `/?id=${result.insertId}`}); 
```
따라서 위의 코드처럼, mysql 패키지의 result.inserId 매소드를 활용해 자동으로 작성한 글 페이지로 리다이렉션 되도록 했다. 

<details>
  <summary>전체 코드</summary>
    
    
    var http = require('http');
    var fs = require('fs');
    var url = require('url');
    var qs = require('querystring');
    var template = require('./lib/template.js');
    var path = require('path');
    var sanitizeHtml = require('sanitize-html');
    var mysql = require('mysql2');

    mypassword = fs.readFileSync('mypassword','utf8');
    var db = mysql.createConnection({
      host:'localhost',
      user:'root',
      password: mypassword,
      database:'physicks'
    });
    db.connect();

    var app = http.createServer(function(request,response){
        var _url = request.url;
        var queryData = url.parse(_url, true).query;
        var pathname = url.parse(_url, true).pathname;
        if(pathname === '/'){
          if(queryData.id === undefined){
            db.query(`SELECT * FROM topic`, function(err, results, fields){
              var title = 'Welcome';
              var description = 'Hello, Node.js';
              var list = template.list(results);
              var html = template.HTML(title, list, 
                `<h2>${title}</h2>${description}`,
                `<a href="/create">create</a>`
              );
              response.writeHead(200);
              response.end(html);
            });
          } else {
            db.query(`SELECT * FROM topic`, function(err, results){
              if(err){
                throw err;
              }
              db.query(`SELECT * FROM topic WHERE id =?`,[queryData.id], function(err2, result){
                if(err2){
                  throw err2;
                }
                var title = result[0].title;
                var description = result[0].description;
                var list = template.list(results);
                var html = template.HTML(title, list, 
                  `<h2>${title}</h2>${description}`,
                  `<a href="/create">create</a>
                    <a href="/update?id=${queryData.id}">update</a>
                    <form action="delete_process" method="post">
                    <input type="hidden" name="id" value="${queryData.id}">
                    <input type="submit" value="delete">
                  </form>`
                );
                response.writeHead(200);
                response.end(html);
              });
            });
          }
        } else if(pathname === '/create'){
          db.query(`SELECT * FROM topic`, function(err, results, fields){
            var title = 'Create';
            var list = template.list(results);
            var html = template.HTML(title, list, `
              <form action="/create_process" method="post">
                <p><input type="text" name="title" placeholder="title"></p>
                <p>
                  <textarea name="description" placeholder="description"></textarea>
                </p>
                <p>
                  <input type="submit">
                </p>
              </form>
            `, `<a href="/create">create</a>`)
            response.writeHead(200);
            response.end(html);
          });
        } else if(pathname === '/create_process'){
          var body = '';
          request.on('data', function(data){
              body = body + data;
          });
          request.on('end', function(){
            var post = qs.parse(body);
            db.query(`
              INSERT INTO topic (title, description, created, author_id) 
                VALUES(?, ?, NOW(), ?)`,
                [post.title, post.description, 1],
                function(error,result){
                  if(error){
                    throw error;
                  }
                  response.writeHead(302, {Location: `/?id=${result.insertId}`});
                  response.end();
                }
              )
          });
        } else if(pathname === '/update'){
          fs.readdir('./data', function(error, filelist){
            var filteredId = path.parse(queryData.id).base;
            fs.readFile(`data/${filteredId}`, 'utf8', function(err, description){
              var title = queryData.id;
              var list = template.list(filelist);
              var html = template.HTML(title, list,
                `
                <form action="/update_process" method="post">
                  <input type="hidden" name="id" value="${title}">
                  <p><input type="text" name="title" placeholder="title" value="${title}"></p>
                  <p>
                    <textarea name="description" placeholder="description">${description}</textarea>
                  </p>
                  <p>
                    <input type="submit">
                  </p>
                </form>
                `,
                `<a href="/create">create</a> <a href="/update?id=${title}">update</a>`
              );
              response.writeHead(200);
              response.end(html);
            });
          });
        } else if(pathname === '/update_process'){
          var body = '';
          request.on('data', function(data){
              body = body + data;
          });
          request.on('end', function(){
              var post = qs.parse(body);
              var id = post.id;
              var title = post.title;
              var description = post.description;
              fs.rename(`data/${id}`, `data/${title}`, function(error){
                fs.writeFile(`data/${title}`, description, 'utf8', function(err){
                  response.writeHead(302, {Location: `/?id=${title}`});
                  response.end();
                })
              });
          });
        } else if(pathname === '/delete_process'){
          var body = '';
          request.on('data', function(data){
              body = body + data;
          });
          request.on('end', function(){
              var post = qs.parse(body);
              var id = post.id;
              var filteredId = path.parse(id).base;
              fs.unlink(`data/${filteredId}`, function(error){
                response.writeHead(302, {Location: `/`});
                response.end();
              })
          });
        } else {
          response.writeHead(404);
          response.end('Not found');
        }
    });
    app.listen(3000);
</details>

7.글수정기능구현(Update)
===
앞선 Create와 거의 동일하다. 데이터를 수정하는 UPDATE 부분만 다시 보자. 
```
 db.query(`UPDATE topic SET title = ?, description = ?, author_id = 1 WHERE id = ?`
          , [post.title,post.description,post.id] , function (...){...}
```
여기서 WHERE을 제대로 안적으면 **모든 데이터가 일괄적으로 바뀐다**. 실수 안하도록 주의하자.


<details>
  <summary> Update 코드</summary> 
    
    } else if(pathname === '/update'){
      db.query(`SELECT * FROM topic`, function(err, results){
        if(err){
          throw err;
        }
        db.query(`SELECT * FROM topic WHERE id =?`,[queryData.id], function(err2, result){
          if(err2){
            throw err2;
          }
          var title = result[0].title;
          var list = template.list(results);
          var html = template.HTML(title, list,
            `
            <form action="/update_process" method="post">
              <input type="hidden" name="id" value="${result[0].id}">
              <p><input type="text" name="title" placeholder="title" value="${title}"></p>
              <p>
                <textarea name="description" placeholder="description">${result[0].description}</textarea>
              </p>
              <p>
                <input type="submit">
              </p>
            </form>
            `,
            `<a href="/create">create</a> <a href="/update?id=${result[0].id}">update</a>`
          );
          response.writeHead(200);
          response.end(html);
        });
      });
    } else if(pathname === '/update_process'){
      var body = '';
      request.on('data', function(data){
          body = body + data;
      });
      request.on('end', function(){
          var post = qs.parse(body);
          db.query(`UPDATE topic SET title = ?, description = ?, author_id = 1 WHERE id = ?`
          , [post.title,post.description,post.id] , function(err,result){
            if (err){
              throw err;
            }
            response.writeHead(302, {Location: `/?id=${post.id}`});
            response.end();
          })
      });
</details>

8.삭제 (Delete)
===
삭제도 앞선 create_process나 update_process에서 사용한 내용들과 같다. DELETE SQL문만 다시 보자  
```
db.query('DELETE FROM topic WHERE id = ?',[post.id], ... )
```
UPDATE와 마찬가지로 WHERE 을 안해주면 값이 전부 바뀐다. 주의할 수 있도록 하자.  

<details>
  <summary> Delete 코드</summary> 
      
      else if(pathname === '/delete_process'){
        var body = '';
        request.on('data', function(data){
            body = body + data;
        });
        request.on('end', function(){
            var post = qs.parse(body);
            db.query('DELETE FROM topic WHERE id = ?',[post.id], function(error, result){
              if(error){
                throw error;
              }
              response.writeHead(302, {Location: `/`});
              response.end();
            });
        });
      }
</details>
  
  
이렇게 해서 4,5 Read \/ 6 Create \/ 7 Update \/ 8 Delete 까지 CRUD 모두 구현해보았다.   
  
<details>
  <summary>8강까지 전체 코드</summary> 
      
    var http = require('http');
    var fs = require('fs');
    var url = require('url');
    var qs = require('querystring');
    var template = require('./lib/template.js');
    var path = require('path');
    var sanitizeHtml = require('sanitize-html');
    var mysql = require('mysql2');

    mypassword = fs.readFileSync('mypassword','utf8');
    var db = mysql.createConnection({
      host:'localhost',
      user:'root',
      password: mypassword,
      database:'physicks'
    });
    db.connect();

    var app = http.createServer(function(request,response){
        var _url = request.url;
        var queryData = url.parse(_url, true).query;
        var pathname = url.parse(_url, true).pathname;
        if(pathname === '/'){
          if(queryData.id === undefined){
            db.query(`SELECT * FROM topic`, function(err, results, fields){
              var title = 'Welcome';
              var description = 'Hello, Node.js';
              var list = template.list(results);
              var html = template.HTML(title, list, 
                `<h2>${title}</h2>${description}`,
                `<a href="/create">create</a>`
              );
              response.writeHead(200);
              response.end(html);
            });
          } else {
            db.query(`SELECT * FROM topic`, function(err, results){
              if(err){
                throw err;
              }
              db.query(`SELECT * FROM topic WHERE id =?`,[queryData.id], function(err2, result){
                if(err2){
                  throw err2;
                }
                var title = result[0].title;
                var description = result[0].description;
                var list = template.list(results);
                var html = template.HTML(title, list, 
                  `<h2>${title}</h2>${description}`,
                  ` <a href="/create">create</a>
                    <a href="/update?id=${queryData.id}">update</a>
                    <form action="/delete_process" method="post">
                      <input type="hidden" name="id" value="${queryData.id}">
                      <input type="submit" value="delete">
                    </form>`
                );
                response.writeHead(200);
                response.end(html);
              });
            });
          }
        } else if(pathname === '/create'){
          db.query(`SELECT * FROM topic`, function(err, results, fields){
            var title = 'Create';
            var list = template.list(results);
            var html = template.HTML(title, list, `
              <form action="/create_process" method="post">
                <p><input type="text" name="title" placeholder="title"></p>
                <p>
                  <textarea name="description" placeholder="description"></textarea>
                </p>
                <p>
                  <input type="submit">
                </p>
              </form>
            `, `<a href="/create">create</a>`)
            response.writeHead(200);
            response.end(html);
          });
        } else if(pathname === '/create_process'){
          var body = '';
          request.on('data', function(data){
              body = body + data;
          });
          request.on('end', function(){
            var post = qs.parse(body);
            db.query(`
              INSERT INTO topic (title, description, created, author_id) 
                VALUES(?, ?, NOW(), ?)`,
                [post.title, post.description, 1],
                function(error,result){
                  if(error){
                    throw error;
                  }
                  response.writeHead(302, {Location: `/?id=${result.insertId}`});
                  response.end();
                }
              )
          });
        } else if(pathname === '/update'){
          db.query(`SELECT * FROM topic`, function(err, results){
            if(err){
              throw err;
            }
            db.query(`SELECT * FROM topic WHERE id =?`,[queryData.id], function(err2, result){
              if(err2){
                throw err2;
              }
              var title = result[0].title;
              var list = template.list(results);
              var html = template.HTML(title, list,
                `
                <form action="/update_process" method="post">
                  <input type="hidden" name="id" value="${result[0].id}">
                  <p><input type="text" name="title" placeholder="title" value="${title}"></p>
                  <p>
                    <textarea name="description" placeholder="description">${result[0].description}</textarea>
                  </p>
                  <p>
                    <input type="submit">
                  </p>
                </form>
                `,
                `<a href="/create">create</a> <a href="/update?id=${result[0].id}">update</a>`
              );
              response.writeHead(200);
              response.end(html);
            });
          });
        } else if(pathname === '/update_process'){
          var body = '';
          request.on('data', function(data){
              body = body + data;
          });
          request.on('end', function(){
              var post = qs.parse(body);
              db.query(`UPDATE topic SET title = ?, description = ?, author_id = 1 WHERE id = ?`
              , [post.title,post.description,post.id] , function(err,result){
                if (err){
                  throw err;
                }
                response.writeHead(302, {Location: `/?id=${post.id}`});
                response.end();
              })
          });
        } else if(pathname === '/delete_process'){
          var body = '';
          request.on('data', function(data){
              body = body + data;
          });
          request.on('end', function(){
              var post = qs.parse(body);
              db.query('DELETE FROM topic WHERE id = ?',[post.id], function(error, result){
                if(error){
                  throw error;
                }
                response.writeHead(302, {Location: `/`});
                response.end();
              });
          });
        } else {
          response.writeHead(404);
          response.end('Not found');
        }
    });
    app.listen(3000);

</details>


---

9.글에 '저자' 출력 (JOIN)
===
topic 테이블과 author 테이블은 author_id와 id로 연결되어 있다. 이 관계성에 따라서, 두 개의 분리된 테이블을 하나로 연결시켜주는 것이 JOIN이다.   
  
![image](https://user-images.githubusercontent.com/101965836/160517972-adcd2bfd-0c43-43ac-b0ac-d73c1678f5fa.png)  
  
  
![image](https://user-images.githubusercontent.com/101965836/160517919-a81565e8-bc0c-40ae-83c2-e699d552db37.png)  
  
```
SELECT * FROM topic LEFT JOIN author ON topic.author_id = author.id;
```

근데 저걸 그대로 넣고 WHERE id = 로 id 값을 가져오면, id column이 두 개 이므로 ambiguous 하다는 에러 메세지를 출력하게 된다.   
  
![image](https://user-images.githubusercontent.com/101965836/160520613-59aac97f-b2ce-411d-8bce-ef2db976106e.png)  
  
  
```JavaScript
db.query(`SELECT * FROM topic LEFT JOIN author ON topic.author_id = author.id WHERE topic.id = ?`,[queryData.id]
```
따라서 위와 같이 쿼리문을 작성하면  
![image](https://user-images.githubusercontent.com/101965836/160520995-1dcc4531-1815-40e8-8cd5-be79a7fb94c2.png)  
이와 같이 result를 얻을 수 있다.  
  
  
그리고 아래에 HTML 문서를 생성하는 부분에서
```JavaScript
var html = template.HTML(title, list, 
  `<h2>${title}</h2>
  ${description}
  <p>by ${result[0].name}</p>
  `,
  ` <a href="/create">create</a>
    <a href="/update?id=${queryData.id}">update</a>
    <form action="/delete_process" method="post">
      <input type="hidden" name="id" value="${queryData.id}">
      <input type="submit" value="delete">
    </form>`
);
```
  
이렇게 <p>by ${result[0].name}</p> 부분을 추가해주면  
![image](https://user-images.githubusercontent.com/101965836/160521169-acb60321-c68e-4ec4-9ed4-6ccc4108116b.png)  
  
이렇게 author.name 까지 페이지에 뜨게된다.  
  
  
---
  
10.글 생성시 저자 입력 (JOIN)
===

Create 페이지에서 저자를 선택 할 ComboBox를 만들 것이다.  

# ComboBox란?
  
![image](https://user-images.githubusercontent.com/101965836/160522166-8083ae93-8ad7-485f-898a-7b1ce1330c16.png)
  
  
```HTML
<p>
  <select name="author">
    <option value="1">egoing</option>
    <option value="2">duru</option>
  </select>
</p>
```
  
위와 같이 쭈욱 author를 선택할 수 있는 리스트가 나오는 것을 만들 것이다.  
  
  
# db.query로 author 데이터 확인  

![image](https://user-images.githubusercontent.com/101965836/160522538-22fc43b9-4b19-4cf3-9d30-067a5808b4dd.png)  
  
author 테이블은 위와 같이 id, name, profile로 구성된다.  
그러면 이제 author를 선택하는 combobox를 만들려면, 위 테이블을 db.query로 가져온 후 name을 순서대로 출력하면 되겠구나 라고 생각할 수 있다.   


# create 부분에 코드 추가  

(1) author 테이블 가져오기

```JavaScript
} else if(pathname === '/create'){
      db.query(`SELECT * FROM topic`, function(err, results, fields){
        db.query(`SELECT * FROM author`, function(err2,authors){
```
create 부분 코드에 다음과 같이 db query로 author를 가져왔다. 


(2) comboBox 태그 생성하는 부분 만들기

먼저 author 테이블에서 id별로 하나씩 author를 꺼내서 comboBox에 들어갈 <option> 태그를 만들고, 변수 tag 에다가 차곡차곡 쌓았다.
```JavaScript
var tag = '';
  var i = 0;
  while(i < authors.length){
    tag += `<option value="${authors[i].id}">${authors[i].name}</option>`;
    i++
  }
```  
  
  
그리고 HTML 태그 생성하는 부분에, author comboBox에 해당하는 태그가 삽입되도록 했다.   
  
```HTML
<p>
  <select name="author">
    ${tag}
  </select>
</p>
```

# template.js에 매소드로 추가
  
```JavaScript
,authorSelect:function(authors){
    var tag = '';
    var i = 0;
    while(i < authors.length){
      tag += `<option value="${authors[i].id}">${authors[i].name}</option>`;
      i++
    }
    return `<select name="author">
              ${tag}
            </select>`
}
```  
  
```
<p>
  ${template.authorSelect(authors)}
</p>
```

# create_process 부분 수정

![image](https://user-images.githubusercontent.com/101965836/160524089-054d5120-3a66-4e3a-9752-8b2545915ac4.png)   
  
지금까지 author_id를 그냥 1로 했는데, 이제 post방식으로 \<select name="author">로 author id가 전달된다. 따라서 1이라고 그냥 적어뒀던것을 post.author로 바꿔주면 된다.  
     
```JavaScript
  
db.query(`
    INSERT INTO topic (title, description, created, author_id) 
    VALUES(?, ?, NOW(), ?)`,
    [post.title, post.description, post.author], ... )
  
```  
   
# 동작
   
![image](https://user-images.githubusercontent.com/101965836/160524642-18ce1ddc-a50a-4785-b1cd-7d1682f34581.png)      
     
![image](https://user-images.githubusercontent.com/101965836/160524692-a8cdba47-ea00-4196-8799-6c125b098369.png)  
    
  잘 동작한다! 
  
