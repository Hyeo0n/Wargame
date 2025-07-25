<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "Enc-JPG"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.07.25 풀이

<img width="592" height="1282" alt="image" src="https://github.com/user-attachments/assets/af967d19-8d77-4203-9ccd-981015e65c98" />

<img width="920" height="262" alt="image" src="https://github.com/user-attachments/assets/c95131a9-8e10-48e3-99e5-6afe16f0340c" />

해당 문제 파일에 알 수 없는 파일 하나랑 jpg인데 열리지 않는 파일이 있어서, 리눅스에서 file 명령어로 살펴보았다.

flag.jpg 파일은 이미지 파일이 아니고, Enc 파일은 EXE 파일인 것으로 보인다.

<br>

</br>

<img width="1266" height="634" alt="image" src="https://github.com/user-attachments/assets/d226453f-e422-4fbe-a6ff-73d39d49049e" />

Enc파일에 .exe를 붙인 뒤, 이를 실행하고 다시 flag.jpg를 살펴보니, PNG라는 헤더가 뜨는 걸로 봐서 이미지 데이터 일부가 복원된 것으로 보인다. 

<br>

</br>

<img width="1058" height="256" alt="image" src="https://github.com/user-attachments/assets/23f0eff0-711a-4a58-aaf2-3a4c00c47aaf" />

해당 이미지를 binwalk로 분석해보니, JPEG와 PNG 파일 모두 확인할 수 있었다. 

<br>

</br>

<img width="1296" height="288" alt="image" src="https://github.com/user-attachments/assets/a884840d-c8f1-44b5-b7d2-d39e57022edb" />

그래서, dd 명령어로 파일을 수동으로 추출해주었다. 

<br>

</br>

<img width="484" height="138" alt="image" src="https://github.com/user-attachments/assets/c751cec1-1b1d-47a7-92c2-ff6b4af54e22" />

이들을 다시 HxD로 각각 살펴보니, JPEG 파일에 푸터가 두 개 있었다. 
FF D9를 하나 지우니, 다음과 같은 DH{How 이미지가 나왔다.
<br>

</br>

<img width="1330" height="416" alt="image" src="https://github.com/user-attachments/assets/eda2d8f2-60ff-4ff9-a3c2-f9d4263b96d5" />

이건 JPEG 파일 끝에 나온 문자열인데, DH{JPG_TXT_PNG} 형태의 flag에서 TXT 부분으로 추정된다.

<br>

</br>

<img width="1250" height="244" alt="image" src="https://github.com/user-attachments/assets/6c8348de-2b31-4be8-94a1-e97d73c87b92" />

다음은 PNG 파일에서 나온 flag의 PNG 부분으로 추정되는 부분이다. 

이들을 모두 조합해보니, 플래그 값이 맞았다.
<br>

</br>

```
DH{How_ENc_ECrypt_yo}
```
<br>

</br>
