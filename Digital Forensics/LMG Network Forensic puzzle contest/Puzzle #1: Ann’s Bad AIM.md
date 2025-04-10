<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> LMS Network Forensic puzaale contest "Puzzle #1: Ann’s Bad AIM"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.04.04 풀이

![image](https://github.com/user-attachments/assets/04258385-a555-4a1b-8c4e-3f3521851027)

☆ Ann의 IP : 192.168.1.158

 
[[문제]]

1. Ann 메신저 대화 상대의 이름은 무엇인가?

2. 캡처 된 IM 대화에서 첫 번째 대화는 무엇인가?

3. Ann이 전송한 파일의 이름은 무엇인가?

4. 추출하려는 파일의 매직 넘버(파일 식별자)는 무엇인가? (처음 4바이트)

5. 파일의 MD5sum은 무엇인가?    

  MD5 (evidence.pcap) = d187d77e18c84f6d72f5845edca833f5

6. 비밀 레시피는 무엇인가?

<br>

</br>  

![image](https://github.com/user-attachments/assets/b4c20651-0297-48eb-b352-f0939ca7b268)

![image](https://github.com/user-attachments/assets/b22c9118-df4d-4c8f-84fe-e928536a8f02)

스터디 실습 시간 중에는 NetworkMiner 로 진행해보았으므로, 이번에는 WireShark로 문제 풀이를 진행해보려고 한다. Ann이 메신저를 주고 받은 부분을 찾으면 되니, Ann의 IP 주소인 192.168.1.158 가 Source이거나 Destination인 부분을 유심히 살펴보려고 한다.

<br>

</br>  

![image](https://github.com/user-attachments/assets/9fc9d8da-c177-4d3b-8b98-675258f8890c)

192.168.1.158과 관련된 패킷 중에 가장 맨 처음에 발견된 출발지가 192.168.1.158인 패킷을 살펴보니, 여러 정보를 담고 있는 헤더 구조를 살펴볼 수 있었다.

<br>

</br> 

![image](https://github.com/user-attachments/assets/401db932-5476-408f-8b43-97a0f255f52d)

![image](https://github.com/user-attachments/assets/ebbe21f0-7197-49e5-9fbd-a59c4c502f4b)

이를 TCP 따라가기로 더 자세히 살펴본 결과,  SSL 통신으로 암호화되어 있어서 전체를 완벽히 확인할 수 없었지만, 중간중간 내용을 파악할 수 있다. 맨 첫 번째에 나온  " Sec558user1 " 이 Ann의 상대방 이름인 것으로 추측된다. 

그리고 해당 IM 대화에서 첫 번째 답글은 가장 위에 있는  " Here's the secret recipe... I just downloaded it

 from the file server. Just copy to a thumb drive and you're good to go " 인 것으로 보인다. 

또한, 중간에 보면 " recipe.docx " 라는 파일명을 확인할 수 있는데, 이는 Ann이 전송한 파일으로 보인다. 

<br>

</br> 


FLAG ▷▶
```
1. Sec558user1
2. Here's the secret recipe. I just download it from the file server. 
   Just copy to a thumb drive and you're good to go.
3. recipe.docx
```

<br>

</br> 
<br>

</br> 

![image](https://github.com/user-attachments/assets/b0185cde-6c6a-4716-bb66-82fff9fd6512)

![image](https://github.com/user-attachments/assets/1fc21e9b-d30c-413a-b694-9809763a543d)

![image](https://github.com/user-attachments/assets/e0c0c6d9-e0ca-4fb3-9c08-2734a11dffb2)

표시 필터 적용 칸에 "frame contains "recipe.docx" " 를 입력하여서, 전체 패킷 데이터에서 recipe.docx 문자열이 포함되는지 확인하고, 포함하는 것들만 출력되게끔 하였다. 그렇게 해서 출력된 4개 중 2번째 거를 TCP 따라가기로 살펴보니, PK ..  라는 시그니처를 확인할 수 있었다. 이를 16진수 덤프 값으로 변환하여 4바이트만 추출하면 " 50 4B 03 04 " 가 해당 파일의 매직 넘버인 것을 확인할 수 있다.

<br>

</br> 
FLAG ▷▶
```
4. 50 4B 03 04
```

<br>

</br> 
<br>

</br> 

![image](https://github.com/user-attachments/assets/b5d0a768-257c-4d83-aff3-64c2c4b0f2cd)

![image](https://github.com/user-attachments/assets/3befe9f1-9a38-4593-8865-8b9a21725d6a)

![image](https://github.com/user-attachments/assets/b8398e95-82ae-4b97-91a5-150f194fe1ce)

![image](https://github.com/user-attachments/assets/333deddb-71dc-4138-b472-006a061137c9)

위에서 발견한 부분을 recipe로 따로 추출하고, 이를 HxD에서 PK 시그니처 앞에 쓸데없는 부분을 삭제시킨 후, 이를 다시 recipe.docx로 저장하였다. 이를 다시 HxD에서 실행하고, 체크섬을 확인해보니, MD-5  체크섬

`8350582774E1D4DBE1D61D64C89E0EA1` 가 해당 파일의 MD5 체크섬인 것을 확인할 수 있다.

<br>

</br> 
FLAG ▷▶

```
5. 8350582774E1D4DBE1D61D64C89E0EA1
```

<br>

</br> 

![image](https://github.com/user-attachments/assets/60581ed1-b79e-465c-b69f-0c373427d8ad)

그렇게 추출한 docx 파일을 열어보니 다음과 같은 래시피를 발견할 수 있었다.
