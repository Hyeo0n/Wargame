<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1>"steg.png" Write-UP</h1>
</body>
<br>
<br>
</html>

2025.03.22 풀이

<img width="1280" height="719" alt="image" src="https://github.com/user-attachments/assets/71c596f7-49ab-4219-8552-b5b77f7e3b7e" />

해당 steg.png 문제를 풀어보려고 한다. 파일 이름부터 steg 인 것처럼 스테가노그래피 문제인 것으로 보인다. 

다양한 도구를 통해 해당 이미지 파일을 분석해보려고 한다. 

<br>

</br>

<img width="1280" height="1021" alt="image" src="https://github.com/user-attachments/assets/d6cc71d4-b202-4b32-8e8f-3303e600a10b" />

FTK Imager를 사용해서, 숨겨진 hex 값이나 text 추출을 시도해보았지만, 숨겨진 값이 없는건지 실행되지 않았다.

<br>

</br>

<img width="1280" height="755" alt="image" src="https://github.com/user-attachments/assets/1dfffc4b-a442-4e91-8a93-f1f7221ece8d" />

<img width="1248" height="418" alt="image" src="https://github.com/user-attachments/assets/81ec2cb8-e89c-476a-9905-3c7ee5fb4dc2" />

그 후, Hxd 를 사용해서, 해당 이미지 파일의 헥스 값을 살펴보았는데, 파일 시그니처 부분과 IHDR, IDAT 청크 부분을 유심히 살펴보았지만 눈에 띄는 점이 없었다. 종종 이미지 파일의 끝을 나타내는 IEND 뒤에 또 다른 파일 확장자를 넣어놓는 문제도 있다고 하길래 살펴보았지만, 해당 부분도 문제점이 될 만한 곳은 보이지 않았다.

<br>

</br>

<img width="1280" height="700" alt="image" src="https://github.com/user-attachments/assets/1cd4ffd5-5791-49f0-b73b-f443ab0e9160" />

그 후, Openstego 툴을 사용해서, 분석해보려고 하였는데, 자꾸 오류가 발생하였다.

 <br>

</br>

<img width="992" height="834" alt="image" src="https://github.com/user-attachments/assets/b0333d3f-a7fd-4f37-9008-570c9d9d369d" />

해당 이미지 파일을 리눅스에 가져와서, strings 명령어로 출력 가능한 문자열을 전부 뽑아내보았다. 하지만, 너무 무지막지하게 길어서 딱히 얻을 수 있는 것은 없었다. 

 <br>

</br>

<img width="502" height="186" alt="image" src="https://github.com/user-attachments/assets/4066479c-3377-48ca-ae43-93361b2b352b" />

뭔가 숨겨진 텍스트가 있지 않을까 하는 생각에 zsteg라는 PNG나 BMP 이미지 파일에 숨겨진 텍스트나 데이터를 자동으로 탐지해주는 도구를 사용해보았다. 해보니, OpenPGP Public Key 라는 문자열이 떴다. 이미지 내부에 공개키 형태의 데이터가 숨겨져있구나! 하고 이를 살펴보기로 하였다.

  <br>

</br>

<img width="420" height="114" alt="image" src="https://github.com/user-attachments/assets/8a83f1ec-e604-4ea4-9561-6e1a7acd0404" />

해당 부분을 asc 형태 파일로 따로 추출하고,

  <br>

</br>

<img width="652" height="554" alt="image" src="https://github.com/user-attachments/assets/be3659c8-8311-4bbe-962b-ce5373f214c9" />

해당 파일을 살펴보니, 그다지 의미가 있어보이지는 않았다. 또한, 살펴보기에도 너무 긴 텍스트였다.

  <br>

</br>

<img width="1272" height="1466" alt="image" src="https://github.com/user-attachments/assets/95274b68-c4c9-4ce1-8c8e-c39727091bef" />

해당 파일 내부에 뭔가 숨겨진 텍스트가 있을 줄 알아서 위와 같이 접근해보았는데도 얻을 수 있는 것이 없어서 막막하였다. 너무 어렵게 생각해서 그런가? 조금 더 간단하게 접근해보자!하고 그림 자체에 보이는 거에 뭔가가 숨겨져 있지 않을까하면서 이미지 조작 관련해서 감지해주는 간단한 웹 기반 도구 forensically에 해당 이미지를 넣어보았다. 여러 부분을 요리조리 살펴보니, 사람 위에 저렇게 "L1nux3rr0r" 라는 숨겨진 문자열을 발견하였다.

  <br>

</br>

<img width="976" height="130" alt="image" src="https://github.com/user-attachments/assets/28d147e4-0084-413c-9295-6c72d783d6de" />

<img width="1280" height="862" alt="image" src="https://github.com/user-attachments/assets/8e54b5cc-04cc-4237-9b92-514283dccb3e" />

뭔가 아쉬워서 위에 forensically 도구 말고, 조금 더 스테가노그래피 탐지하는데 많이 쓰이는 도구 Stegsolve를 통해서 해당 이미지를 살펴보았다.

 <br>

</br>

<img width="1280" height="738" alt="image" src="https://github.com/user-attachments/assets/a2b932f7-cc05-4a94-863c-a9a4cee17243" />

<img width="1280" height="758" alt="image" src="https://github.com/user-attachments/assets/ad931b55-ad3d-4308-a2fb-9104c565c336" />

<img width="1280" height="760" alt="image" src="https://github.com/user-attachments/assets/65f27c67-7cc7-4e06-b1a1-0f5f2b57afe6" />

여러 필터를 하나하나 넘기다 보니, 사람 가슴팍 부분에 아까 발견한 "L1nux3rr0r" 문자열을 볼 수 있었다.

 <br>

</br>
 <br>

</br>

FLAG 
```
L1nux3rr0r
```
