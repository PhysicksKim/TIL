# Remember Me Token 구조 

```
USERNAME  	SERIES  	TOKEN  	LAST_USED  
helloUserId	r2PRPzt1T5L+E5Dv5BjzFA==	xk91w2jj+jdL+OHnIDXkOw==	2024-04-04 15:37:19.536
```

여기서 username 은 User 의 식별자(UsernamePasswordAuthentiction 에서 회원 로직의 식별자)  
  
그런데 Series 와 token 은 각각 무슨 용도일까?  
  
# {series}:{token} 구조로 사용자에게 제공한다  

RememberMe cookie 는 value 만 제공하는 게 아니라 series 와 value 를 같이 제공한다.  

```
1. series + token 결합  
{series}:{token}
```
```
2. base 64 로 인코딩 후 cookie 에 담아서 전송
```

### 예시

```
series = "M/YTpI6IcQQyf9qeQ3zz8A=="
token  = "8k/RbGGasfBaDb3ZsAS1JQ=="
```

```
"series:token"
M/YTpI6IcQQyf9qeQ3zz8A==:8k/RbGGasfBaDb3ZsAS1JQ==
```

```
base64 로 인코딩 결과  
TS9ZVHBJNkljUVF5ZjlxZVEzeno4QT09OjhrL1JiR0dhc2ZCYURiM1pzQVMxSlE9PQ==
```

<br><br> 

# 이렇게 하는 이유?  

## 탈취 감지를 위하여  

series 는 사용자&기기 별로 유일한 값이다  
예를 들어 physicks 유저의 PC 와 mobile 에서는 각각 다른 series 가 적용되며  
token 의 value 값은 변경될 수 있지만 series 는 한 번 생성되면 계속 유지된다.  
  
만약 공격자가 해당 remember-me 쿠키를 탈취해서 사용하다가  
토큰 순환에 의해서 value 값이 변경되었다고 해보자.  
  
```
1. 사용자 토큰 {abc}:{123}
2. 공격자가 탈취해서 계속 사용 {abc}:{123}
3. 사용자에게 바뀐 토큰 제공 {abc}:{456}
4. 공격자가 잘못된 토큰으로 요청 {abc}:{123}
5. DB 에 있는 토큰과 불일치를 감지하고, 비정상적인 요청으로 인식하여 모든 series=abc 인 토큰을 만료시킴  
```


  

