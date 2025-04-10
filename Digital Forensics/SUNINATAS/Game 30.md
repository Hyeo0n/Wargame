<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> SUNINATAS "Game 30"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.03.29 풀이

![image](https://github.com/user-attachments/assets/41045dcc-c001-4d06-8f79-c1a0565b0907)

위와 같은 써니나타스 문제를 풀어보려고 한다. 아마 1,2,3 형식을 따라가서 각각 얻은 값을 조합하면 플래그 값을 얻을 수 있을 것으로 보인다.

<br>

</br>  

![image](https://github.com/user-attachments/assets/332e8265-0f25-44e7-a322-fa13bcd7562f)
..........
![image](https://github.com/user-attachments/assets/eef864df-093f-42ae-9afa-65d215a691fe)

volatility의 windows.info로 본격적으로 문제를 풀기 앞서 준비를 하였다.

<br>

</br> 
<br>

</br> 

![image](https://github.com/user-attachments/assets/c70f4cee-d3c4-4310-a23d-6ffb64d15fb7)

첫 번째로, IP 주소를 구해야 하기 때문에 네트워크와 프로세스 상태를 분석하는 플러그인 netscan을 사용하여, 분석하였다. 대부분이 192.168.197.138에서 출발하기 때문에, `192.168.197.138` 가 PC의 IP 주소인 것을 알 수 있다.

<br>

</br> 
<br>

</br> 

![image](https://github.com/user-attachments/assets/36192730-567e-442f-b009-0097df218d13)

windows.cmdline 를 통해 살펴보니, SecreetDocumen7.txt가 메모장을 통해 실행되었던 것을 발견할 수 있었다. 

<br>

</br> 
<br>

</br> 

![image](https://github.com/user-attachments/assets/2fbe528e-6647-4dee-a678-026ef5f0e0ec)


해당 문서를 복구하기 위해, windows.filescan을 입력하여, 해당 txt 파일의 메모리 주소를 알아낼 수 있었다.


 <br>

</br> 

![image](https://github.com/user-attachments/assets/c73f8c8a-7c1a-4fef-a8b8-b7450c84be5d)

위에서 알아낸 메모리 주소를 통해, 해당 파일을 복구할 수 있었다.

 <br>

</br> 
<br>

</br>  

![image](https://github.com/user-attachments/assets/4df12d8c-d205-4267-8852-68141340f4f1)

해당 파일을 메모장으로 살펴보니, Key 값은 "4rmy_4irforce_N4vy" 라는 메모를 발견할 수 있었다. 

<br>

</br>  

따라서, 

1번 답인 192.168.192.138, 

2번 답 SecreeyDocumen7.txt 

3번 답 4rmy_4irforce_N4vy을 합쳐서

`lowercase(MD5(192.168.197.138SecreetDocumen7.txt4rmy_4irforce_N4vy))` 이 플래그 값인 것을 알 수 있다.

MD5가 있는 것으로 보아, 조합한 문자열을 MD5 문자열로 암호화해주면, 

C152E3FB5A6882563231B00F21A8ED5F 로

그리고 lowercase로 인해 대문자를 소문자로 바꿔주면

c152e3fb5a6882563231b00f21a8ed5f 최종 플래그 값을 얻을 수 있었다. 


<br>

</br>  

FLAG ▷▶

```
c152e3fb5a6882563231b00f21a8ed5f
```
