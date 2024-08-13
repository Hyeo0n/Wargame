<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "web-ssrf"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.08.14 풀이

![image](https://github.com/user-attachments/assets/7e3f2456-0ccd-4137-9fcd-af5b301090a3)

https://chs777888.tistory.com/76
에서 SSRF에 대해서, 학습한 후,
이를 실습으로 익히기 위해, Dreamhack 워게임에 있는 2단계의
web-ssrf를 풀어보려고 한다. 

 <br>
 
</br>

![image](https://github.com/user-attachments/assets/68dc896b-2256-4b77-a8ac-5bb90bb41277)

![image](https://github.com/user-attachments/assets/ee70581f-05b1-4c4c-8cd5-7a6bd44497f4)

![image](https://github.com/user-attachments/assets/c8964454-c3a0-44b2-aa5b-2ba9039060bd)


우선 서버에 접속해보니, Image Viewer라는 링크가 있고, Image Viewer를 들어가보니, 다음과 같은 화면이 있었다. 입력칸에 기본적으로 /static/dream.png 라는 경로가 입력되어 있고, view 버튼을 누르면 드림핵 로고가 뜨는 다음의 화면이 출력된다. 

 <br>
 
</br>

![image](https://github.com/user-attachments/assets/e063aabe-02bd-4824-a4c5-24ab0a20ba8b)

![image](https://github.com/user-attachments/assets/1605d4e6-51c5-41a0-878d-84630096f4b0)

About, Contact 페이지는 처음 Home 페이지와 같이 그냥 Image Viewer가 뜬다. 이제 문제 파일을 살펴보려고 한다.

<br>
 
</br>
<br>
 
</br>

## app.py

![image](https://github.com/user-attachments/assets/acd3621f-4f12-4adc-b3db-7e1353142b8b)
![image](https://github.com/user-attachments/assets/df3d0641-e2fb-448e-ba4b-7b7b5b088d55)
![image](https://github.com/user-attachments/assets/56fbc4bf-78f0-43ec-9a41-e45829ff5357)

## 
<br>
 
</br>

![image](https://github.com/user-attachments/assets/94c538f6-f181-4f81-af80-5aa4d346ba5f)

해당 문제 코드 중 위 부분을 살펴보니, 현재 디렉토리에 flag.txt 파일이 있는 것을 알 수 있다.
<br>
 
</br>

## 
<br>
 
</br>

![image](https://github.com/user-attachments/assets/ae360d35-bb29-40a2-b676-c361377f46ef)

``/img_viewer`` 페이지의 POST 메소드 부분을 보면, 일단 사용자로부터 입력받은 url을 가져오는 것을 알 수 있다. url이 / 로 시작된다면, url은 "http://localhost:8000"에 입력받은 url을 합쳐지는 것을 알 수 있다. 또한, 입력받은 url에 "localhost"나 "127.0.01"이 있다면, 에러 이미지를 화면에 띄우고, 함수를 종료하는 내용을 확인할 수 있었다. 함수가 종료되지 않으면, data 값에 localhost에 위치한 url 파라미터에 해당하는 경로의 값을 가져오고, 해당 값을 base64로 디코딩하여 이미지를 출력하는 것을 확인할 수 있다.
<br>
 
</br>

## 
<br>
 
</br>

![image](https://github.com/user-attachments/assets/b7f98d64-dd3a-411f-8f11-50b09291514a)
localhost IP는 127.0.01이고, 포트는 1500~1800 사이에 랜덤으로 있는 것을 알 수 있다.
<br>
 
</br>
맨 처음 문제 설명을 보면, flag.txt에서 flag 값을 구할 수 있을 것 같은데, flag.txt는 localhost의 IP인 127.0.0.1을 사용해야지 접근할 수 있을 것 같다.

``http:127.0.01: [port] /flag.txt`` 형식을 대입하야 할 것 같지만, 127.0.01이 필터링이 되고, 포트번호도 모르기 때문에 이 문제점을 해결하고 flag 값을 구해보려고 한다.

## flag
일단 127.0.01가 필터링되지 않게, 127.0.01의 hex 값인 ``0x7f000001`` 을 활용할 생각이다. 그리고 포트 번호를 확인하기 위해, 파이썬 코드를 작성해서 포트 번호를 알아내보려고 한다. 
<br>
 
</br>

![image](https://github.com/user-attachments/assets/39508b7a-c45c-4389-b95e-3bcbefbc8bb5)

1500~1800 로 범위를 대입하여, 포트 번호를 알아내는 파이썬 코드를 작성해보았다. 
이를 터미널에서 실행시켜 보니,
<br>
</br>

![image](https://github.com/user-attachments/assets/1df5f14c-433d-4ac6-826c-08b14592de65)

포트 번호가 ``1544`` 라는 것을 알았다. 이제 이를 활용하여 입력칸에 입력해보려고 한다. 
<br>
 
</br>

``http://0x7f000001:1544/flag.txt`` 

<br>
 
</br>

![image](https://github.com/user-attachments/assets/b7cb4a3a-c897-4424-9367-081c50ca660a)

![image](https://github.com/user-attachments/assets/c00f5ad7-b67f-409a-92f1-7ffadfa60a95)

입력해보니, 뭔가 달라지는 것이 없어서 개발자 도구를 확인해보니, 기존에 보이지 않았던 태그가 추가된 것이 보였다.
뭔가 base64로 인코딩되어 있는 것 같아서, 이를 디코딩해보니,

<br>
</br>

```
DH{43dd2189056475a7f3bd11456a17ad71}
``` 

<br>
</br>

flag 값을 얻을 수 있었다

<br>

</br>

![image](https://github.com/user-attachments/assets/58be6add-1cf6-4254-8635-bc9f9f2f257e)

이렇게 문제를 풀이할 수 있었다.
