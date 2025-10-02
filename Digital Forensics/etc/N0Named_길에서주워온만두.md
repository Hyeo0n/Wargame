<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> N0Named "길에서 주워온 만두" Write-UP</h1>
</body>
<br>
<br>
</html>

2025.03.22 풀이

<img width="1280" height="1018" alt="image" src="https://github.com/user-attachments/assets/d9465171-1b69-4b7d-8452-bdb33529025d" />

<img width="939" height="993" alt="image" src="https://github.com/user-attachments/assets/047ae8c2-6200-426e-8b9c-041e4aa45ea7" />

해당 길에서 주워먹는 만두 문제의 big.png를 살펴보려고 한다. 문제 이름은 길에서 주워먹는 만두인데 사진은 마트료시카 사진이 나왔다. 바로 위 문제 steg.png랑은 다르게, 먼저 보이는 이미지 자체에 뭔가 숨겨져 있는지 살펴보았다. 

<br>

</br>

<img width="1182" height="1492" alt="image" src="https://github.com/user-attachments/assets/57128163-ed7b-46d8-9b7a-14b06ef3e200" />

<img width="1280" height="1146" alt="image" src="https://github.com/user-attachments/assets/12941f70-87ec-4dd4-adfe-f978f6b815b6" />

forensically, Stegsolve 둘 다로 확인해보았지만, 딱히 발견된 것 없었다. 

<br>

</br>

<img width="1280" height="1054" alt="image" src="https://github.com/user-attachments/assets/c955d8db-e08a-4fd8-8881-5b75c61ed85e" />

Hxd로 살펴보니, 헤더 부분은 시그니처 그대로 였고, IEND 뒤에 부분에 PASS:1234라고 쓰여있었다. 어디에 쓰는 건지는 확실히 알 수 없었다. 

<br>

</br>

<img width="1280" height="702" alt="image" src="https://github.com/user-attachments/assets/162aa6fe-53c7-4f44-92ff-307d663397b6" />

<img width="1280" height="680" alt="image" src="https://github.com/user-attachments/assets/27013827-170b-4017-9295-d4f01d84899c" />

<img width="1280" height="616" alt="image" src="https://github.com/user-attachments/assets/6b52ad64-e7b4-4504-8b16-07fb6e646ab5" />

Openstego 툴에 실행해보니, 오류가 발생한다. 그래서, 아까 위에서 얻은 1234를 혹시나 해서 입력해보니, medium.png 파일이 새롭게 생겨났다.

<br>

</br>

<img width="1178" height="1134" alt="image" src="https://github.com/user-attachments/assets/a4c3b175-cf83-49af-aec0-ab1c092a0640" />
열어보니, 아까와 똑같은 사진이 나왔다.

<br>

</br>

<img width="1280" height="1057" alt="image" src="https://github.com/user-attachments/assets/202e3dd0-06c3-4f1d-85ae-2833ea2931de" />
이를 또 Hxd에서 살펴보니, 뭔가 특별한 것은 없었다.

<br>

</br>

<img width="1280" height="703" alt="image" src="https://github.com/user-attachments/assets/37951b54-5ad3-4303-848e-136d85b64afc" />
이게 마트료시카 사진이기도 하고, 처음에는 big.png였다가 medium.png가 생겼었으니까, 한 번만 더 OpenStego 도구를 사용하면 이번에는 small.png가 나오지 않을까? 하는 생각이 들었다. 그래서 이번에는 medium.png를 사용해서 해보았다.

<br>

</br>

<img width="1280" height="700" alt="image" src="https://github.com/user-attachments/assets/2336a48a-5ada-4053-b66a-9c3c1aaf81a4" />

<img width="1280" height="652" alt="image" src="https://github.com/user-attachments/assets/844af9ab-691d-498c-9e2e-f0a4cb3f3209" />

예상대로 small.png가 생겼다! 이번에도 똑같은 마트료시카 사진이 나왔다.

아까와 medium.png 때와 똑같이 뭔가 특별한 게 발견되지 않았다.

<br>

</br>

<img width="1280" height="702" alt="image" src="https://github.com/user-attachments/assets/d08fb2f6-0db8-450c-8e8e-0f9f3bab2f3e" />
그래서, 위와 같은 과정을 small.png에도 혹시 몰라서 반복해보았다.

<br>

</br>

<img width="1280" height="705" alt="image" src="https://github.com/user-attachments/assets/307d085b-a14d-4a2e-a8da-2e0950c1fb67" />

<img width="1280" height="664" alt="image" src="https://github.com/user-attachments/assets/d98697b2-1d0c-4c92-9931-6e29df647786" />

해보니, flag.txt를 얻을 수 있었다!

<br>

</br>

<img width="1190" height="338" alt="image" src="https://github.com/user-attachments/assets/a6a9d46f-2f7b-49df-9ee0-b25b525ea269" />




<br>

</br>

FLAG
```
NND{Matryoshka_&&_OPENSTEGO!>.<}
```
