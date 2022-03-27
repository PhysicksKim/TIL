# 2중 포문
[출처](https://blog.naver.com/bitcoding/221546891537)  
  
정리
## 1. flag 이용하기
```
isRight = True

for ... :
  for ... :
    # 2중으로 탈출 해야 하는 경우
    if ??? == (...) : 
      isRight = False
      break
    
  if not(isRight):
    break
```

## 2. 함수의 return 이용하기
```
def fun() :
    for i in range(5):
        for j in range(5):
            if i == 1 and j == 1:
                return
```
