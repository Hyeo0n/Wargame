<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "Firmware Extraction Practice"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.07.19 풀이

<img width="990" height="720" alt="image" src="https://github.com/user-attachments/assets/f5402cc5-1cc5-4ace-a76b-6041c444426e" />

<img width="1280" height="571" alt="image" src="https://github.com/user-attachments/assets/9df0027e-5a77-42fc-8a72-3389f464e7c1" />

문제 파일을 다운받은 후, binwalk로 펌웨어를 언팩한다. 간단히 시그니처, 펌웨어 구조 등이 출력되는 것을 확인할 수 있다.

<br>

</br> 

<img width="1280" height="229" alt="image" src="https://github.com/user-attachments/assets/d1646f8b-fdbd-4b54-80cb-74ec07c8f86d" />

추출된 파일을 살펴보니, 펌웨어가 잘 추출된 것을 확인할 수 있다.

<br>

</br> 

<img width="1280" height="249" alt="image" src="https://github.com/user-attachments/assets/5c186ee6-91ac-4ff4-bf8d-3f9b9c847534" />

/etc/shadow 파일을 열여보니, dream 계정만 패스워드가 설정된 것을 알 수 있었다. 

해시 형태는 $Algorithm$salt$hash로, MD5를 사용한 것처럼 보인다. 

<br>

</br> 

<img width="1280" height="672" alt="image" src="https://github.com/user-attachments/assets/85faf9e4-873a-4d1e-85c6-33d28056b4ba" />

이를 John the Ripper 도구를 사용하여, 분석해보려고 한다. unshadow 명령어로

passwd와 shadow를 하나의 파일로 합치고, john 명령어를 통해, 해시 크래킹을 시도한다. 

<br>

</br> 

<img width="1280" height="281" alt="image" src="https://github.com/user-attachments/assets/74badc67-5d92-4119-b759-10ef18c7e460" />

그 후, init.d에 rcS를 살펴보니, flag에 관한 말이 나온다. 

그리고, startup.sh를 실행하는 명령이 이어서 나온다.

startup.sh을 출력해보니, shadow 파일에 있는 정보로 복호화한 패스워드를

sha256으로 해싱한 값이 플래그인 것을 알 수 있었다.

<br>

</br> 

```
DH{72ab994fa2eb426c051ef59cad617750bfe06d7cf6311285ff79c19c32afd236}
```
