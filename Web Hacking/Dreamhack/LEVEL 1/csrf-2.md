<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "csrf-2"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.08.01 풀이

![image](https://github.com/user-attachments/assets/e10b9bb5-c966-4c9f-9b4a-7e705d0dd1bb)

https://chs777888.tistory.com/47

에서 CSRF에 대해서, 학습한 후,

이를 실습으로 익히기 위해, Dreamhack 워게임에 있는 1단계의

csrf-2를 풀어보려고 한다. 

 <br>
 
</br>

![image](https://github.com/user-attachments/assets/e57170c8-b7ce-4ec3-a4d4-2a91d7876aa7)

우선 서버에 먼저 들어가보았다. csrf-1 문제와 비슷해보이지만, login 페이지가 있고, please login이라는 말이 출력되어 있는 걸 보니, login 관련해서 해결해야 하는 문제 같다.

  <br>
  
</br>

![image](https://github.com/user-attachments/assets/ec18ca49-b91e-48ae-ae6d-2c65309fd7f2)

vuln 페이지에 들어가보니, 이는 csrf-1과 유사하게, <*>alert(1)이 출력되어 있다. 위에 파라미터에 script 구문이 사용된 걸 봐선, 아마 이번에도 script 구문을 필터링하는 코드가 사용되지 않았나 싶다.

  <br>
  
</br>

![image](https://github.com/user-attachments/assets/d119db5d-3c2d-4d60-8d3a-4077bc2c59ab)
 
flag 페이지를 들어가보니, 역시 vuln 페이지로 접속하게끔 하고, 뒤에 내용을 적는 입력칸이 있다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/084a728f-25e4-4a94-9ee6-59e12c41e803)
 
login 페이지에 들어가보니, 평범한 id와 비밀번호를 입력하는 로그인 창이 나온다.

이제 코드를 살펴보려 한다.

<br>

</br>

![image](https://github.com/user-attachments/assets/62f72c61-b1fb-425f-a3ac-e4eeec7ad663)

![image](https://github.com/user-attachments/assets/f8502e1d-cf76-46c3-9b8d-092fe27f8239)

![image](https://github.com/user-attachments/assets/f20ccd6c-40cf-4201-97c4-8eacb278d0c4)

![image](https://github.com/user-attachments/assets/4c6e0a9d-2944-45e1-8a80-e1363951365b)

코드를 살펴보니,

<br>

</br>
 
![image](https://github.com/user-attachments/assets/9dda538c-f0fe-443d-be9e-57bdb51404b5)

이 부분을 통해, 아이디-비번 쌍이 guest-guset와 admin-flag 인 것을 확인할 수 있다. admin 비밀번호가 중요한 관건이 될 것 같다.

실제로 서버에서 guest-guest로 로그인해보니, 

<br>

</br>
 
![image](https://github.com/user-attachments/assets/de57831d-4c26-4667-b6f9-b52c43eb05ae)

로그인되는 것을 확인할 수 있다. 

 <br>
 
</br>

## vuln 페이지
![image](https://github.com/user-attachments/assets/e1ea8166-7c2d-4298-8037-5d2545f58255)

flag 페이지 코드 부분을 살펴보니, session_id가 랜덤으로 생성된 후, admin가 쌍을 이루어 저장되는 것으로 보인다. 그리고, 이것들이 모두 check_csrf에 전달되는 것 같다.

check_csrf를 살펴봐야 겠다.

 <br>
 
</br> 

## check_csrf
![image](https://github.com/user-attachments/assets/d45c4125-e759-46c5-836f-aeacdfbf08e6)

살펴보니, 아까 전달된 쌍으로 이루어진 값들이 url에 저장되는 것을 확인할 수 있다. 그리고, 이 url이 실행되는 내용인 것 같다.

 <br>
 
</br>  

 ## change_password 페이지

![image](https://github.com/user-attachments/assets/5fd92b48-b05c-46fb-947f-0b676c226c8c)

그리고, 기존에 서버에서 발견할 수 없었던 change_password 페이지를 코드에서 발견할 수 있었다. 코드를 보니, 입력한 값을 새롭게 비밀번호로 지정할 수 있는 내용 같았다. 

이 페이지를 이용해서, admin 아이디의 비밀번호를 내가 임의로 변경한 후에, 로그인 해보면, flag 값이 나올 것 같다고 생각했다. 

그래서, flag 페이지 입력칸에 csrf-1에서처럼 img 태그를 사용해서, change_password 페이지로 이동해서, 비밀번호를 내 임의로 변경하는 내용을 넣으면 될 것 같다는 생각이 들었다.

 <br>
</br>  

```
<img src="/change_password?pw=admin">
``` 

이렇게 작성한 것을 아까 guest로 로그인한 서버에 입력해보니, 

  <br>
  
</br>  

![image](https://github.com/user-attachments/assets/20b9dc32-434b-4ded-9bbd-5ff246a7a3c6)
![image](https://github.com/user-attachments/assets/6b294497-76d7-4b9b-b2d7-0a8e1a4fc4a7)

good 알림창이 뜨고,

<br>
</br>  

![image](https://github.com/user-attachments/assets/404b855b-acf1-47bf-8108-918fddd4644e)

다시 로그인 창에 가서, admin-admin으로 로그인을 해보았다.

<br>
</br>  

![image](https://github.com/user-attachments/assets/cd727b10-5973-47b8-8307-a8a66aa72cd3)

로그인 시도가 성공된 후, 화면에 flag가 출력되었다. 

 <br>
</br> 

```
DH{c57d0dc12bb9ff023faf9a0e2b49e470a77271ef}
``` 
 <br>
</br> 

![image](https://github.com/user-attachments/assets/870f34f3-324f-4cb5-85e5-654888cacc92)
 
이렇게 문제를 해결할 수 있었다.


