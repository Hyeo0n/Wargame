<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "patch"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.07.25 풀이

<img width="672" height="1066" alt="image" src="https://github.com/user-attachments/assets/7d43faee-7470-47b4-8847-9d9022b96563" />


<img width="1168" height="380" alt="image" src="https://github.com/user-attachments/assets/014a9f36-4cc5-4032-b2ad-fee39e19dcf9" />

문제 파일을 받고, 그 안에 patch.exe 파일이 있어서 이를 실행해보니, 다음과 같은 화면이 나온다.
<br>

</br> 

<img width="1722" height="1152" alt="image" src="https://github.com/user-attachments/assets/0d6ac299-3b4d-4422-ba35-88af6f54f266" />

해당 파일을 x64dbg에서 실행해보니, 해당 부분에서 플래그 값으로 추정되는 값이 검정색으로 덮혀진 문구가 있는 창이 나왔다.

<br>

</br> 

<img width="1144" height="204" alt="image" src="https://github.com/user-attachments/assets/7c3e3887-e65f-4c4a-adc4-590a45a25de2" />

해당 부분 안에 다음과 같이 구성되어 있었는데, <ShowWindow>에서는 patch.exe의 실행창을 띄워주고, <UpdateWindow>에서는 기존의 흰 실행창에 한 획씩 그림을 그려주는 준다.

<br>

</br> 

<img width="892" height="246" alt="image" src="https://github.com/user-attachments/assets/7348f69f-3869-459d-85bd-0c8818b7ae5b" />
<img width="1158" height="369" alt="image" src="https://github.com/user-attachments/assets/942dd28c-c506-427a-a119-48d09cbc667c" />

해당 <UpdateWindow>를 F7로 한 단계씩 실행하면, 다음과 같이 차근차근 그림이 그려진다.
이를 통해 flag 값을 가리는 것으로 보인다.

<br>

</br> 
<br>

</br> 

<img width="1910" height="1290" alt="image" src="https://github.com/user-attachments/assets/b76e09f8-2382-4973-950f-7280ddf8a362" />

IDA에서 해당 exe를 살펴서, 다음과 같은 main 함수를 발견하였다. v11부분에서 사이즈, 스타일을 정의하는 것을 보아 그림 그리는 부분이 것 같아 보인다.

<br>

</br> 

<img width="1250" height="1304" alt="image" src="https://github.com/user-attachments/assets/c639aaa1-884c-4fc7-bb31-519d881a5fcc" />

가장 먼저 나온 함수인 sub_1400032F0를 살펴보니, 이 역시 BeginPaint, EndPaint 등등 함수 이름과 코드 구조를 보아, BeginPaint와 EndPaint 사이에서 그림이 그려지는 것으로 보인다. 

<br>

</br> 

<img width="1108" height="1340" alt="image" src="https://github.com/user-attachments/assets/ac3ad52b-0c10-4efc-8ba6-7dee11439be9" />

그 둘 사이에 있는 sub_140002C40를 확인하였다. 위쪽 부근은 그림을 그려서 검정색 가려지는 부분을 실행하는 부분 같고, 밑 부분은 flag를 화면에 보여주는 부분 같아 보인다.

<br>

</br> 
<br>

</br> 

<img width="1458" height="630" alt="image" src="https://github.com/user-attachments/assets/abfeb786-9558-4227-92d3-ba5e1f429f50" />

따라서 위에 검정색 그림이 그려지는 부분인 sub_140002B80 함수의 가장 첫 줄에 ret으로 수정해주어서, 해당 함수가 실행되지 않도록 하였댜.
<br>

</br> 

<img width="938" height="208" alt="image" src="https://github.com/user-attachments/assets/972dc797-209c-4c78-ab8a-623e0e788094" />

이렇게 flag값을 얻을 수 있었다. 


```
DH{UPATCHED}
```


