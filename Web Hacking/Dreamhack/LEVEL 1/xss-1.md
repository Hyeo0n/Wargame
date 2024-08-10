<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "xss-1"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.07.05 풀이

![image](https://github.com/user-attachments/assets/ec434226-c34f-49cb-93ea-1e73161d06d0)
 
https://chs777888.tistory.com/44

에서 XSS에 대해서, 학습한 후, 

이를 실습으로 익히기 위해, Dreamhack 워게임에 있는 1 단계의 

xss-1 를 풀어보려고 한다.

<br>
</br>

일단 해당 서버에 접속해보니, 세 페이지가 나온다.

<br>

</br>

![image](https://github.com/user-attachments/assets/f3415924-3ee9-4d13-977a-aea6967c3cae)

## vuln 페이지 접속 화면
![image](https://github.com/user-attachments/assets/259559b8-e4c5-4ac4-a01c-f58369dce9b1)

## memo 페이지 접속 화면
![image](https://github.com/user-attachments/assets/9e58de20-a5ab-4ef0-ab82-f89fa373f8b3)

## flag 페이지 접속 화면
![image](https://github.com/user-attachments/assets/93f2242e-48a1-4e8c-913e-f011685fe926)

flag 페이지를 보니, 스크립트 언어를 입력해야 할 것처럼
보이는 입력칸이 있는 것을 볼 수 있다.

<br>
</br> 

그 후, 문제를 다운받은 후, 압축을 풀어보니, 여러 파일들이 있었는데,

<br>

</br>
 
![image](https://github.com/user-attachments/assets/a1d14b5e-2eac-4029-ac49-203dd2884192)

static과 templates 에 있는 파일들은 css와 관련된 파일 같아서 우선적으로

app.py 파이썬 파일 먼저 살펴보려고 한다.

이를, visual studio code로 실행시켜 보니, 아래와 같은 코드들이 보였다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/1e3e30dc-308c-4df9-9b16-ee83851940d5)

![image](https://github.com/user-attachments/assets/d53646e3-d69f-4e0c-b2e4-f598bddf2377)

![image](https://github.com/user-attachments/assets/e80c63c5-1c99-4ec7-b816-b967e68625c9)

위 코드들을 살펴보니, 

<br>
</br>
 
![image](https://github.com/user-attachments/assets/11e50c7e-c84e-49b2-9809-ea295841d997)


해당 코드 부분은 vuln 페이지를 구성하는 코드로 보인다. 코드를 간략히 해석해보면

, 전달받은 param을 출력하는 코드 같다. param이 뭔지 찾아보니, 서버와 통신을 할 때 넘겨주는 파라미터 클래스라고 한다. 즉, 이 코드는 이용자로부터 받은 param을 출력하는 코드같다.

 <br>
 
</br>

![image](https://github.com/user-attachments/assets/e1887759-2182-45c6-ab34-6f351d5b4fc5)

해당 코드 부분은 memo 페이지를 구성하는 코드로 보인다. 코드를 간략히 해석해보면

, memo_text라는 전역 변수를 정의하고, request.args.get 부분을 보면, 이용자로부터 받은 memo 입력값을 가져오고, 이를 memo_text에 저장하는 것 같다. 그리고, memo.html 즉, memo 페이지에 기록된 memo_text 화면에 출력하는 것으로 보인다. 이 출력하는 과정에서 render_template라는 함수(?)가 사용된 것 같다.

  <br>
 
</br>

![image](https://github.com/user-attachments/assets/1dc40705-d6b6-4651-9db2-93ec7e487780)

해당 코드 부분은  flag 페이지를 구성하는 코드로 보인다. 코드를 간략히 해석해보면

, 요청 메서드가 GET이면, flag.html 템플릿을 출력하게끔 하는 것 같고,

요청 메서드가 POST이면, 이용자로부터 전달받은 폼 데이터에서

param 이라는 이름의 값을 가져오는 것 같다. 그리고, if not check_xss 부분을 통해,

param이 FLAG와 맞지 않으면, 아래 코드를 실행하여,

알림창으로 wrong??이라는 말을 출력하고,

아니면, 알림창으로 good 이라는 말을 출력하게끔 하는 코드 같다.

일단 flag 부분에 check_xss 함수가 꽤나 중요한 관건 같아서, check_xss 함수를 살펴보려고 한다.

   <br>
 
</br>

![image](https://github.com/user-attachments/assets/91a01239-c050-448d-963e-03ec5e6d56c7)

해당 부분이 함수 check_xss 정의 코드 부분인데, 일단 이 함수는

param과 cookie 두 개를 매개변수로 받는 것 같다.

그리고, cookie는 name과 value로 채워진 딕셔너리로 정의되어 있는 것을 알 수 있다. 

이걸 보니까, 아까 check_xss에서 

 if not check_xss(param, {"name": "flag", "value": FLAG.strip()}):

이 부분에서 딕셔너리가 flag로 되어 있는 것을 보니,

cookie 값에 flag 값이 저장되는 건가 라는 생각이 들었다.

일단 뒤에 코드를 다시 보자.

그 뒤에 url 부분이 잘 해석되지 않아, 찾아보니까, urllib.parse.quote() 은 ()안에 값을 URL로

인코딩하여, 안전한 형태로 변환시킨다고 한다. 

즉, param 값을 URL로 인코딩하여, 변환하는 것 같고

, 이를 http://127.0.0.1:8000/vuln로 전달하는 것 같다. 그리고 read_url 함수에

변환된url을 매개변수로 전달하는 것 같다 

read_url 함수를 한 번 살펴보겠다.

   <br>
 
</br>

![image](https://github.com/user-attachments/assets/ca5863b9-b79c-42f8-b8c0-50acc115eeb3)

driver.get('http://127.0.0.1:8000/') 을 통해, 해당 웹 페이지를 접속하고, 

driver.add_cookie(cookie)을 보면, 매개변수인 cookie를 추가하는 것 같이 보인다.

driver.get(url) 를 통해, 주어진 URL로 이동하는 것 같다. 

이 url이 check_xss에서 보았던 param이 저장된 url인 것을 알 수 있다. 

뭔가 이 함수에서 127.0.0.1:8000인 도메인에 쿠키가 생성이 되는 것 같다. 

   <br>
</br>

쿠키에 flag 값이 심어지는 것 같다는 생각이 들었고,

이 쿠키는 http://127.0.0.1:8000/ 에 있으므로, 이 주소에서

사용되는 쿠키 값을 알아내면 될 것 같다는 생각이 들었다. 

이 부분에서, 어떻게 코드를 작성하여,

flag 값을 얻어내야 할 지 감이 안 잡혀서,

드림핵 강의에서 이 워게임 설명글을 

보면서, 힌트를 얻어냈다. 

location.href와 document,cookie를 사용한다는 식으로 

글이 써져 있길래, 찾아보니

location.href은 전체 URL을 반환하거나, URL을 업데이트하는 것이고

, document,cookie는 해당 페이지에서 사용하는 쿠키를 읽고, 쓰는

속성값이라고 한다.

이들을 활용하여, 메모장에 cookie 값이 기록이 되게끔 하는 코드를 

작성해보았다. 

```
<script> location.href="http://127.0.0.1:8000/memo?memo=hello"+document.cookie; </script>
```

   <br>
</br>

![image](https://github.com/user-attachments/assets/ecb7d0b5-e7a4-4ccc-a84c-f15c9cda403b)

![image](https://github.com/user-attachments/assets/34002007-c79f-496f-8d1f-b479ff4b9e60)

이를 flag 페이지에 입력해보니, good 알림창이 뜬 것을 확인할 수 있다.

   <br>
   
</br>

 ![image](https://github.com/user-attachments/assets/6779fc75-e967-4090-b675-766ca6c0df8c)

그 후, 메모 페이지를 가보니, flag 값이 기록된 것을 확인할 수 있다.....!

   <br>
</br>

![image](https://github.com/user-attachments/assets/3d2472dd-18e5-4564-bf73-4c049c861243)


 ```
flag=DH{2c01577e9542ec24d68ba0ffb846508e}
```

 <br>
 
</br>

 내가 쓴 라이트업을 앞에서 부터 다시 보니, 진짜 갈팡질팡 의식의 흐름대로 쓴 듯 하다,,,,
파이썬 소스 코드가 너무 복잡했던 것 같ㄷㅏㄹ


