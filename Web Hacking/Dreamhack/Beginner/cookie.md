<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "cookie"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.05.18 풀이

![image](https://github.com/user-attachments/assets/34068b96-c875-480b-b0df-6adcc8c7a880)

https://chs777888.tistory.com/21 

에서 쿠키에 대해서, 학습한 후, 

이를 실습으로 익히기 위해, Dreamhack 워게임에 있는 비기너 단계의 

cookie 를 풀어보았다.

문제를 다운받은 후, 압축을 풀어보니, 파이썬 코드가 나왔다. 

이를, visual studio code로 실행시켜 보니, 아래와 같은 코드들이 보였다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/015f5a85-ce2d-4181-a135-842919860b7c)

![image](https://github.com/user-attachments/assets/c1f0993c-4783-4ae0-af14-f50eee01521a)

문제 파일에서 코드를 살펴보니, user (사용자)는 guest, admin 총 2개있는 것을 확인할 수 있었다.

<br>

</br>

 ![image](https://github.com/user-attachments/assets/147f6de9-bf66-497b-84b0-4ab17c068ebb)

해당 부분을 보니, admin 사용자로 로그인하면, 화면에 flag가 출력되고, 아니면 (guest면), you are not admin이 출력되는 것을 확인할 수 있었다, 

그리고, 실제 접속 화면에 들어가보았다.

<br>

</br>

![image](https://github.com/user-attachments/assets/a5f6ed83-3424-4714-95b7-9b6ca7829655)

들어가서, 우클릭 후, 페이지 소스 보기를 통해, HTML 문서를 확인하였다.

<br>

</br>

![image](https://github.com/user-attachments/assets/a2581c04-aea4-4072-9894-0d5c2ce4d4c8)

![image](https://github.com/user-attachments/assets/e01a831d-997d-4709-b582-7989a87d6176)

HTML 문서를 보니, # default account: guest / guest 라고 쓰여있는 주석을 통해, guest 사용자 계정 정보를 확인할 수 있었고, 이를 실제 접속 사이트에서 로그인 해보니, 로그인되는 것을 확인할 수 있었다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/f8560fcd-901e-4ada-8cd5-cfbb1ac55c6b)

![image](https://github.com/user-attachments/assets/1831511f-1b27-4a1e-a674-eff05f03951b) 

그리고, guest 계정으로 로그인할 때, 개발자 도구를 이용하여, Application → 쿠키 → 접속사이트 주소를 클릭하니, guest계정으로 로그인했을 때의 쿠키값을 얻을 수 있었다. 이 서버에 로그인 요청한 사용자가 admin이어야지 flag값을 알 수 있으니까, 현재 로그인한 사용자가 admin이라고 속이기 위해, 그냥 구글 application에서 username에 해당하는 쿠키값을 admin으로 수정해봐야 겠다고 생각했다. 

 <br>

</br>

![image](https://github.com/user-attachments/assets/e8891217-b714-4ae6-b343-9714d4630223)

![image](https://github.com/user-attachments/assets/51cf73d4-4cde-46e8-8f41-d8b8b78e248a)

쿠키값을 admin으로 바꾼 후, 접속 사이트에서 새로고침해보니까, admin으로 로그인되면서, Flag 값이 출력되었다!

 <br>

</br>

```
DH{7952074b69ee388ab45432737f9b0c56}
``` 
 <br>

</br>

![image](https://github.com/user-attachments/assets/d069d29d-dc0e-414f-8e36-e3bb27690124)

성공!!


