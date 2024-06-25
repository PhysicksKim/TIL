[해결법 출처](https://velog.io/@ppinkypeach/Docker-Compose%EB%A1%9C-Mysql-%EC%8B%A4%ED%96%89-%EC%8B%9CAccess-denied-for-user-rootlocalhost-using-passwordYES-%ED%95%B4%EA%B2%B0)  

# 문제  

maria db docker 실행 후 로그인하려 했는데  
자꾸 password 가 틀려서 denied 됐다.  

### 도커 컨테이너 실행 docker-compose up
```
Physickskim-MacBook-Air resources % docker-compose up -d
[+] Running 3/3
 ✔ Network resources_default     Created                                                                                          0.0s
 ✔ Container redis-scoreboard    Started                                                                                          0.2s
 ✔ Container mariadb-scoreboard  Started                                                                                          0.1s
Physickskim-MacBook-Air resources % docker-compose ps
NAME                 IMAGE                COMMAND                   SERVICE   CREATED         STATUS         PORTS
mariadb-scoreboard   mariadb:latest       "docker-entrypoint.s…"   mariadb   6 seconds ago   Up 6 seconds   0.0.0.0:3306->3306/tcp
redis-scoreboard     redis:7.2.4-alpine   "docker-entrypoint.s…"   redis     6 seconds ago   Up 6 seconds   0.0.0.0:6379->6379/tcp
```
  
### mariadb 도커로 접근해 로그인 시도
```
gimhongchan@Physickskim-MacBook-Air resources % docker exec -it mariadb-scoreboard bash
root@794b4447d47a:/# mariadb -u root -p
Enter password:
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
```

자꾸 access denied 됐다.  
  
### 도커 compose 내용
docker-compose.yml
```
services:
  redis:
    image: redis:7.2.4-alpine
    container_name: redis-scoreboard
    command: redis-server --requirepass 1234 --port 6379
    ports:
      - "6379:6379"

  mariadb:
    image: mariadb:latest
    container_name: mariadb-scoreboard
    environment:
      MARIADB_ROOT_PASSWORD: 1234
      MARIADB_DATABASE: scoreboard
      MARIADB_USER: dev
      MARIADB_PASSWORD: dev1234
      TZ: Asia/Seoul
    ports:
      - "3306:3306"
    volumes:
      - mariadb_data:/var/lib/mysql

volumes:
  mariadb_data:
    driver: local
```

왜 비밀번호가 틀렸다고 하는걸까?  
  
<br><br>  

# 해결 : 도커 볼륨 삭제 후 재빌드  
  
도커 볼륨 삭제   
```
docker-compose down -v
```
  
재빌드    
```
docker-compose up --build 
```
    
### 로그인 성공  
```
root@fa9e599ab46b:/# mariadb -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 11.4.2-MariaDB-ubu2404 mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

마침내 로그인에 성공했다.  
  
<br><br>  

# 원인 추측 : 아마 이전에 했던 비밀번호 설정과 꼬였던 것 같다  
아마도 이전에 이 pc(mac air)에 docker 를 이용해 mysql 계열을 설치하면서 생긴 여러 옵션 중 하나가  
도커 mariadb password 에도 영향을 미쳐서 그런 것 아닐까?  
  
볼륨 삭제 후 재빌드해서 성공했다는 것은   
이전에 도커 볼륨에서 설정이 꼬였는데, 그걸 자꾸 그대로 써서 문제가 됐나보다.  
  
