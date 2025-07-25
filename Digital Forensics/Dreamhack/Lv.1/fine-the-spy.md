<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "find-the-spy"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.03.29 풀이

![image](https://github.com/user-attachments/assets/ca110b41-384a-4936-b705-46f7fe4dbfa6)

해당 드림핵 find-the-spy 풀이를 진행해보려고 한다.

<br>

</br>  

![image](https://github.com/user-attachments/assets/9f537e2a-9ed3-4f25-9acb-4ac2c1db862e)

일단 volatility의 windows.info로 프로파일 정보를 출력해보았다.

<br>

</br>  


![image](https://github.com/user-attachments/assets/a11e3278-a2a1-40e0-8983-3fac2bf41d0a)

![image](https://github.com/user-attachments/assets/517fdd10-6f38-4a5d-bac7-60d8935f3f67)

![image](https://github.com/user-attachments/assets/c6162066-5b5a-4aaf-9d20-c6081c062454)

그 후, A가 압축한 파일을 찾기 위해, windows.filescan으로 살펴보니, A가 압축한 파일로 의심되는 reports.zip 파일 기록을 2개 발견할 수 있었다. 

<br>

</br> 
<br>

</br> 

![image](https://github.com/user-attachments/assets/52e5699e-c6ec-4240-a342-ffafdf087916)

이들을 각각 따로 덤프하면 진행이 안되길래, &&를 사용하여 같이 진행해보니, 무사히 덤프하여 reports.zip 파일을 추출할 수 있었다.
 
<br>

</br> 

![image](https://github.com/user-attachments/assets/3d355d73-17da-49a1-b1b3-ee44769cc670)

추출된 DAT 파일 HxD로 살펴보니, PK 시그니처가 잘 있는 것을 확인할 수 있었다.

<br>

</br> 

![image](https://github.com/user-attachments/assets/5eed6b85-014b-4342-aa57-4f1250fd122c)

![image](https://github.com/user-attachments/assets/841c02c2-74e3-4c5b-b09e-17e11db4b30e)

그러던 중, 밑에 부분을 살펴보니 PNG의 헤더와 푸터 시그니처를 발견하였다. ZIP 파일 끝부분에 PNG를 숨겨놓은 것으로 보인다.
<br>

</br> 

 ![image](https://github.com/user-attachments/assets/07f1e36c-4a79-4e59-9fdf-4856720740f4)

해당 PNG 부분을 선택하여, 따로 추출한 후, 해당 PNG를 살펴보니, 위와 같은 PNG를 얻을 수 있었다.

<br>

</br>
<br>

</br>
FLAG ▷▶

```
DH{240131120000_COEX}
```
