# Squirrel vs NSIS  
    
## Squirrel  
  
### 장점 
oneClick install 을 지향함  
백그라운드 설치로 사용자 경험이 좋음  
  
### 단점  
Squirrel.windows 는 이제 유지보수 되지 않음. 마지막 릴리즈가 2020년  
커스텀이 어려움. 심지어 설치 경로 설정도 어려움  
electron builder 에서 더 이상 지원하지 않음(deprecated)  
  
<br><br>  

## NSIS 

### 장점  
각종 커스텀에 좋음.  
흔히 exe 파일로 설치하면 볼 수 있는 네모 회색 UI 들이 NSIS임  
electron builder 에서 여전히 지원하고, windows 설치 패키징에 NSIS 사용하기를 권장함  
  
### 단점  
UI 못생김  
업데이트시에도 설치 패키지 UI를 띄우는 점이 못생김(아마 커스텀 가능하지 않을까 추측중)    
  
<br><br>  
  
# 결론 : NSIS 쓰자  
일단 NSIS 를 사용하기를 권장하고  
실제로 많은 앤터프라이즈급 프로그램도 NSIS 를 그대로 사용하곤 하니까  
일단 NSIS 그대로 사용해도 나쁠 거 없어 보임
  
