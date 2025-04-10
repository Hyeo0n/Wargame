<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> SUNINATAS "Game 30"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.04.04 풀이

![image](https://github.com/user-attachments/assets/ea43d52b-dd73-4e64-a0e2-e1d1779324c3)

ZZANGHACKER 라는 사람이 비밀번호를 잊어버렸다고 한다.

<br>

</br>  

![image](https://github.com/user-attachments/assets/b4d7d157-12ee-45e6-b306-b289cc73bbc2)

![image](https://github.com/user-attachments/assets/e40fccfa-c25b-4d9b-b039-f8eaa816db15)

주로 로그인, 회원가입과 같이 비밀번호를 입력해서 서버로 보내는 경우에는 http 기반의 요청으로 이루어진다고 한다. 그래서, 위에 표시 필터 적용 칸에 http를 입력하여, http 패킷들만 살펴보려고 한다. 
<br>

</br>

![image](https://github.com/user-attachments/assets/ee29a9ea-d7a0-4f86-948c-f922d74e047a)

![image](https://github.com/user-attachments/assets/c6f25225-0912-4196-a426-88e23a6f9948)

비밀번호가 포함된 통신은 HTTP 기반 중에서도 주로 HTTP POST 요청으로 이루어진다고 해서, post 요청을 살펴보니, 여러 다양한 아이디를 가진 사람들의 회원가입으로 추정되는 패킷들을 살펴볼 수 있다.

<br>

</br>

![image](https://github.com/user-attachments/assets/befa2fb2-c4a9-441c-9ef0-3360fad1ba43)

그 다음 POST 요청을 확인하니, IMZZANGHACKER 아이디의 회원가입을 발견할 수 있었다.

<br>

</br>

![image](https://github.com/user-attachments/assets/36a0d819-912d-4334-bbaa-2cad2f891d3c)

![image](https://github.com/user-attachments/assets/49f2088d-a26a-414b-8649-0ab089680f4e)

이를 우클릭 후, TCP 스트림 따라가기로 살펴보니, 어떤 Data를 주고받았는지 조금 더 자세히 살필 수 있었다. Join Success! 라는 문구가 있는 것을 보니, IMZZANGHACKER의 비밀번호는 `IDISLIE` 라는 것을 알 수 있었다.
<br>

</br>

![image](https://github.com/user-attachments/assets/6901357c-ca1b-4cda-a918-de11cce7625b)

![image](https://github.com/user-attachments/assets/7b32847a-269d-4926-8ccc-ab6c77e91843)

여담으로, 밑에 또 다른 POST 요청을 살펴보니, ZZANGHACKER로 다른 비밀번호로 시도하니, check your id or pw 라고 뜨는 것을 확인할 수 있다.

<br>

</br>
<br>

</br>
FLAG ▷▶

```
IDISLIE
```

