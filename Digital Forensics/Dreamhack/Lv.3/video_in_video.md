<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "video_in_video"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.07.25 풀이

<img width="590" height="908" alt="image" src="https://github.com/user-attachments/assets/533fd401-e674-49f3-a2a6-caa773a4c0fe" />


<img width="1358" height="798" alt="image" src="https://github.com/user-attachments/assets/dac0405e-6c91-4d28-b1e6-fac7b2780414" />

<img width="1248" height="648" alt="image" src="https://github.com/user-attachments/assets/21c52d97-2aab-47c6-a49a-09a4311e4bd1" />
<img width="1210" height="618" alt="image" src="https://github.com/user-attachments/assets/49c8823d-dce2-4469-831d-599ee0502918" />

문제 파일을 다운받으니, 한가위 이미지가 보인다. 이를 HxD로 살펴보니, 푸터 뒤에 MP4의 시그니처인 ftyp를 확인할 수 있었다.

<br>

</br>

<img width="1058" height="218" alt="image" src="https://github.com/user-attachments/assets/7a6c961a-3dbd-4f8c-9fb8-4e153b82c783" />

<img width="1256" height="254" alt="image" src="https://github.com/user-attachments/assets/6f3f0f78-3eb5-4e64-8118-9f8d389ed511" />

이를 binwalk로 분석한 후, dd 명령어로 MP4를 추출하였다.


<br>

</br>

<img width="774" height="464" alt="image" src="https://github.com/user-attachments/assets/7aca3b4a-7d90-46c9-bbde-4e943a0438d2" />
<img width="224" height="498" alt="image" src="https://github.com/user-attachments/assets/22277210-f237-4b98-ad5f-e03bb2b8ede5" />

추출한 동영상을 MP4 분석 툴인 DAE-MMF-Parser을 다운받아, 해당 영상 구조를 살펴보았다.

보통의 mp4 파일은 mdat에서 끝나야 하는데, 그 뒤에 이상한 영역이 하나 더 있는 것을 발견하였다.
이걸 또 다른 mp4 파일이라고 생각하고 이를 또 추출해주었다.

<br>

</br>

<img width="555" height="293" alt="image" src="https://github.com/user-attachments/assets/35aaaa0b-d47f-40d2-9e39-897c37c366ae" />

해당 추출한 파일을 재생시켜보니, 재생이 도무지 되지 않았다. 다른 사람의 라이트업을 살펴보니 
일반 재생 플레이어에서는 재생이 안 되고 KM Player 라는 재생 플레이어에서만 된다길래 이를 다운받아 보니 플래그를 얻을 수 있었다. 

<br>

</br>

```
BISC{codec_based_carving}
```

