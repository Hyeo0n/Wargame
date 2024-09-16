<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "session-basic"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.05.18 풀이

![image](https://github.com/user-attachments/assets/824ff2a2-b398-4684-9990-3d9b53484bab)

https://chs777888.tistory.com/21 

에서 세션에 대해서, 학습한 후, 

이를 실습으로 익히기 위해, Dreamhack 워게임에 있는 1 단계의 

session-basic 를 풀어보았다.

문제를 다운받은 후, 압축을 풀어보니, 파이썬 코드가 나왔다. 

이를, visual studio code로 실행시켜 보니, 아래와 같은 코드들이 보였다. 

 <br>

</br>

![image](https://github.com/user-attachments/assets/01d53311-6473-46d1-80c4-dfe62d95b8a1)

![image](https://github.com/user-attachments/assets/ceb96356-a895-4710-971d-0dc026a3b221)

![image](https://github.com/user-attachments/assets/1d309395-140c-4ae9-81b2-4c70627d33c8)

문제 파일에서 코드를 살펴보니, user(사용자)는 guest, admin, user 총 3개 있는 것을 확인할 수 있었다.

  <br>

</br>

![image](https://github.com/user-attachments/assets/215f5f5f-0e54-4778-b184-149a057b37ed)

해당 부분을 보니, admin 사용자로 로그인하면, 화면에 flag가 출력되고, 아니면 (guest면), you are not admin이 출력되는 것을 확인할 수 있었다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/0647385c-4666-46d4-ac08-5c0ca22e1bfd) 

이 부분을 통해, 반환하는 값은 session_storage 값이고, 이 session_storage 안에 admin 로그인 세션 데이터가 있을 것이라고 생각했다. .

그리고, 저 app.route('/admin')을 통해, admin이라는 페이지가 있구나 하고 깨달았다. 

그리고, 실제 접속 화면에 들어가보았다.

<br>

</br>
 
![image](https://github.com/user-attachments/assets/18042b1e-fb30-4097-91ae-0547cf322b1e)

들어가서, 우클릭 후, 페이지 소스 보기를 통해, HTML 문서를 확인하였다.

<br>

</br>

![image](https://github.com/user-attachments/assets/a2b38712-f19b-4c2e-9497-b4c58271f447)

HTML 문서를 보니, # default account: guest / guest 라고 쓰여있는 주석을 통해, guest 사용자 계정 정보를 확인할 수 있었고, 이를 실제 접속 사이트에서 로그인 해보니, 로그인되는 것을 확인할 수 있었다. 

 <br>

</br>

![image](https://github.com/user-attachments/assets/9db979f0-46ec-49f2-b57e-2f4cc85f9eca)

![image](https://github.com/user-attachments/assets/c0d45871-3988-48a9-91ea-27cd67f3751b)

그리고, guest 계정으로 로그인할 때, 개발자 도구를 이용하여, Application → 쿠키 → 접속사이트 주소를 클릭하니, guest계정으로 로그인했을 때의 쿠키값을 얻었고, 그 외에도 사용자 guest의 세션 ID도 확인할 수 있었다. 

<br>
</br>

그리고, 아까 소스 코드 분석할 때 찾은 admin 페이지에 들어가기 위해, 기존의 접속 사이트 주소에 /admin을 추가해서, 들어가보았다.

<br>

</br>

![image](https://github.com/user-attachments/assets/2492a34c-f76e-4d6d-9d36-e5dfff68d874)

들어가보았는데, 아까 개발자 도구로 확인할 수 있었던 guest의 세션 ID 와 admin 계정의 세션 ID를 확인할 수 있었다. 

이 admin 계정의 세션 ID를 복사해서 아까 guset 로그인 했었을 때 확인한 개발자 도구 쿠키 창에서 세션 ID 값이 붙여넣기 해보도록 하겠다. 

<br>

</br>


![image](https://github.com/user-attachments/assets/8c583a08-ec00-4c78-b85f-73fc31731135)

세션값을 admin으로 바꾼 후, 접속 사이트에서 새로고침해보니까, admin으로 로그인되면서, Flag 값이 출력되었다!

<br>

</br> 

![image](https://github.com/user-attachments/assets/6587c39b-ba8b-42da-a612-955adb473fb3)
 
성공!!
