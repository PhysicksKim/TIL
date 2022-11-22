# DropDown 작동안함  

---

<img width="521" alt="image" src="https://user-images.githubusercontent.com/101965836/203341762-b22d99ad-fb61-47e4-8dcf-2625d7fec17f.png">

---

이렇게 Boards 를 누르면 드롭다운 메뉴가 나와야 하는데  

---

<img width="737" alt="image" src="https://user-images.githubusercontent.com/101965836/203342095-2d78cef8-d531-4f4f-bfe9-ff2b6249bd7a.png">  
  
---

login 메뉴에서는 Boards를 눌러도 드롭다운 메뉴가 안나왔다  
  
<br><br><br>  

# 해결  
login.html 파일에 bootstrap용 js를 넣어주지 않아서 발생한 에러였다  
  
```html
...
<body>
  ...
  
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.2.0-beta1/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```
  
이렇게 끝에다가 bootstrap이 정상 작동하도록 해주는 js 파일을 넣어줘야한다.  
위 js파일이 여러 bootstrap의 기능들이 작동하도록 해주는데   
드롭다운도 js로 동작하는 기능중 하나다  
