# 1. 업데이트 시 창 안 띄우도록
[공식문서 appUpdater.quitAndInstall() 에서 isSilent](https://github.com/electron-userland/electron-builder/blob/b01d5225631115f6f301cb113b044fd10ebb5256/docs/auto-update.md#module_electron-updater.AppUpdater+quitAndInstall)     
     
isSilent 를 true 로 해주면 조용하게 업데이트 해준다    
추가로   
```
appUpdater.quitAndInstall(true, true)  // isSilent, isForceRunAfter
``` 
이와 같이 isForceRunAfter 해주면 업데이트 이후에 창을 띄워준다.  
  
<br>  

# 2. 윈도우 사용자 선택 없이 인스톨  

> v25
```
"nsis": {
  "perMachine": true,
},
```
  
perMachine 을 true 로 하면, 설치할 사용자를 묻지 않고 전체 사용자에 대해서 설치를 진행한다.    
