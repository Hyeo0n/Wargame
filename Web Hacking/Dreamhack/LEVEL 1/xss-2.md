<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "xss-2"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.07.05 풀이

![image](https://github.com/user-attachments/assets/b404888d-c50e-42db-97c0-53437b9f7694)

https://chs777888.tistory.com/45

에서 xss-1 문제를 푼 후, 

조금 더 xss에 대해서, 학습하기 위해

Dreamhack 워게임에 있는 1 단계의 

xss-2 를 풀어보려고 한다.

<br>
</br>
 
일단 해당 서버에 접속해보니, xss-1과 비슷하게 세 페이지가 나온다.

![image](https://github.com/user-attachments/assets/76992046-2b03-42cb-a33d-d1aa8e9cc46c)

![image](https://github.com/user-attachments/assets/515df7ae-fb1b-4672-b90b-70cff4e61197)


다만 조금 다른 점은, vuln 페이지에서 1이라는 말을 하는 알림창이 뜨지 않는 것이다.

 <br>
</br>

![image](https://github.com/user-attachments/assets/e8dca5df-d444-4389-a5b5-388942c63962)

![image](https://github.com/user-attachments/assets/f7536c37-c97f-484e-829c-175c79991b51)

그 후, 문제를 다운받은 후, 압축을 풀어보니, 여러 파일들이 있었는데,

그 파일 중, app.py 파이썬 코드를 열어보았다.

<br>

</br>

![image](https://github.com/user-attachments/assets/d24f4298-b5d0-40c5-81c0-e9408e689bb8)

![image](https://github.com/user-attachments/assets/3ebff2b9-a31e-45fd-aa30-c1ad0e032f81)
 
![image](https://github.com/user-attachments/assets/db40ef78-e164-4631-8cf7-c5e23a335231)


위의 소스 코드를 보니, vuln에서 param을 return 하는 코드가 빠진 거 외에는

xss-1과 똑같은 코드였다.

 <br>
</br>

저번 xss-1에는 1이라는 알림창을 뜨게 하기 위해

`<script>alert(1)</script>` 이 있었는데,

이번에는 `<script>alert(1)</script>`이 있는데도,

이 스크립트 태그가 작동하지 않는 것을 확인할 수 있다. 

스크립트 태그 대신에 다른 것을 입력해야 할 것 같다.

이를 우회해서 할 수 있는 방법이 뭐가 있을까하고 인터넷을 찾아본 결과,

해당 스크립트 태그 대신에,

<svg onload=>를 사용하는 경우들을 발견하였다.

 <br>
</br>

이를 참고하여, vuln 페이지 주소창에 `<svg onload="alert('1')">`을

뒤에 이어붙이니까,

  <br>
  
</br>

![image](https://github.com/user-attachments/assets/0f685328-0881-4ddd-be5b-73782fbbc13d)

다시 1이라는 알림창이 생겼다. 

그래서, 전에 xss-1에서 썼던 코드를 <svg onload=> 형식으로

작성하였다.

 <br>
</br>

```
<svg onload="location.href = 'http://127.0.0.1:8000/memo?memo=' + document.cookie">
```

 <br>
</br>

 ![image](https://github.com/user-attachments/assets/7a0011c6-442c-4c75-9984-f7bd691a9501)

![image](https://github.com/user-attachments/assets/bc4d5054-62cd-455e-a7b4-b0cad747a480)

이를 flag 페이지에 입력해보니, good 알림창이 뜬 것을 확인할 수 있다.

<br>

</br> 

![image](https://github.com/user-attachments/assets/e3f64ee2-fe8f-45f9-a0e6-42dea1aa530f)

그 후, 메모 페이지를 가보니, flag 값이 기록된 것을 확인할 수 있다.....!

 <br>

</br>

```
flag=DH{3c01577e9542ec24d68ba0ffb846508f}
``` 
 <br>
</br>

![image](https://github.com/user-attachments/assets/7633ddaf-c82f-41da-b009-02af6d126bbc)

 


