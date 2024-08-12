<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "image-storage"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.08.12 풀이
![image](https://github.com/user-attachments/assets/7f582bab-0644-4093-8c98-2ad7e6fbc922)

https://chs777888.tistory.com/73
에서 File Vulnerability에 대해서, 학습한 후,

이를 실습으로 익히기 위해, Dreamhack 워게임에 있는 1 단계의

image-storage를 풀어보려고 한다.

<br>
</br>

일단 해당 서버에 접속해보니, 이미지를 업로드하고 공유하라는 문구가 뜬다.

![image](https://github.com/user-attachments/assets/a32dfbc8-3062-4aab-a492-2d405e824387)

<br>

</br>

## list 페이지 접속 화면
![image](https://github.com/user-attachments/assets/c3bf2108-0b08-4ccf-95b5-40eae3e0fcc8)

list 페이지에는 아무것도 뜨지 않는다.

<br>

</br>

## Upload 페이지 접속 화면
![image](https://github.com/user-attachments/assets/b056bbdf-b2af-4e78-bddb-77c1e2fd52ad)
파일을 업로드하도록 되어 있다. 이제 코드를 한 번 살펴보려고 한다.
<br>

</br>  

문제 파일을 다운받아보니, 세 개의 php 파일이 있었다.
![image](https://github.com/user-attachments/assets/130ff492-517f-4c17-a676-41e23e81a282)

<br>

</br>

## upload.php

![image](https://github.com/user-attachments/assets/f5dfb2bb-9ed2-40b5-8a1f-1a48dda8de44)

![image](https://github.com/user-attachments/assets/0e1df873-89ea-4fc8-99c6-ff0a7a6121e9)

upload.php 파일을 살펴보니, 파일을 업로드받는데, 이때 파일 확장자등에 필터링이 존재하지 않는다.
이로 인해 file uplaod vulnerability에 취약한 것을 알 수 있다. 

<br>

</br>

## list.php
![image](https://github.com/user-attachments/assets/644549d4-7e5a-4dc9-9e6a-74737f06c3f7)

list.php 파일을 살펴보니, 업로드 디렉토리를  "./uploads/"로 설정하고, scandir 함수를 통해, 디렉토리의 파일을 스캔하는 것을 볼 수 있다. 그 후, ``<li>`` 태그를 통해, 디렉토리 내의 각 파일에 대해 링크가 포함된 목록 항목을 출력하는 것을 알 수 있다. 

<br>

</br>

upload 페이지에서 필터링하는 제약이 딱히 없으니, flag 값을 출력하게끔 하는 코드가 저장된 파일을 생성한 후, 이를 upload 페이지에 업로드한 후, list 페이지에서 해당 파일에 접근하여, flag 값을 얻으면 될 것 같다는 생각이 들었다. 

<br>

</br>

<br>

</br>

## PHP 파일 작성

![image](https://github.com/user-attachments/assets/5992d99f-96df-4569-8bd2-069fc061521d)


맨처음 문제 설명에 flag는 /flag.txt에 있다고 했기 때문에, flag.txt를 출력하는 코드를 php 파일로 작성하였다.

<br>

</br>
이를 upload 페이지에 업로드해보니,

![image](https://github.com/user-attachments/assets/4670bf77-ebf9-49ea-a475-b72dab6082fb)
![image](https://github.com/user-attachments/assets/640dbbf7-8757-483b-882b-282a39391707)

업로드 디렉토리 uploads에 Flag.php가 저장되었다고 뜬다.
<br>

</br>

![image](https://github.com/user-attachments/assets/d6b69992-b95c-4419-b3bb-4d5c1c8dc074)
list 페이지에 이동해보니, Flag.php 파일이 목록 형식으로 링크가 달려있었다.
해당 링크로 이동해보니, 
<br>

</br>

![image](https://github.com/user-attachments/assets/baa9cd97-38c9-4e9f-a17a-29ce49dee9af)
flag 값이 출력되었다! 

<br>

</br>

```
DH{c29f44ea17b29d8b76001f32e8997bab}
```

<br>

</br>

![image](https://github.com/user-attachments/assets/08fdd8d1-3a95-47db-a03a-47e32f7194dd)
이렇게 문제를 해결할 수 있었다.

