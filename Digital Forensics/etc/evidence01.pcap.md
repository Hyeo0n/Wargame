<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> "evidence01.pcap" Write-UP</h1>
</body>
<br>
<br>
</html>

2025.04.04 풀이

<img width="794" height="1116" alt="image" src="https://github.com/user-attachments/assets/f8d51854-b4f8-47b7-bb87-0c7f2e0edace" />

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

<img width="1280" height="693" alt="image" src="https://github.com/user-attachments/assets/ed549ff0-9d75-463d-b512-c487b1ee07c7" />

<img width="1280" height="432" alt="image" src="https://github.com/user-attachments/assets/309e6ca5-f82b-4bba-a248-b7308b1f0e78" />

스터디 실습 시간 중에는 NetworkMiner 로 진행해보았으므로, 이번에는 WireShark로 문제 풀이를 진행해보려고 한다. Ann이 메신저를 주고 받은 부분을 찾으면 되니, Ann의 IP 주소인 192.168.1.158 가 Source이거나 Destination인 부분을 유심히 살펴보려고 한다.

<br>

</br>

<img width="1280" height="928" alt="image" src="https://github.com/user-attachments/assets/43df68ef-a668-4896-a121-2d4a8b3d7d1d" />

192.168.1.158과 관련된 패킷 중에 가장 맨 처음에 발견된 출발지가 192.168.1.158인 패킷을 살펴보니, 여러 정보를 담고 있는 헤더 구조를 살펴볼 수 있었다.

<br>

</br>

<img width="1280" height="735" alt="image" src="https://github.com/user-attachments/assets/54aa78db-2920-45af-902f-469d73173979" />

<img width="1280" height="770" alt="image" src="https://github.com/user-attachments/assets/63524cae-6ccf-4929-861f-58b7b2ca45c5" />

이를 TCP 따라가기로 더 자세히 살펴본 결과,  SSL 통신으로 암호화되어 있어서 전체를 완벽히 확인할 수 없었지만, 중간중간 내용을 파악할 수 있다. 맨 첫 번째에 나온  `Sec558user1` 이 Ann의 상대방 이름인 것으로 추측된다. 

그리고 해당 IM 대화에서 첫 번째 답글은 가장 위에 있는  `Here's the secret recipe... I just downloaded it
 from the file server. Just copy to a thumb drive and you're good to go` 인 것으로 보인다. 

또한, 중간에 보면 `recipe.docx` 라는 파일명을 확인할 수 있는데, 이는 Ann이 전송한 파일으로 보인다. 

<br>

</br>

<img width="1280" height="550" alt="image" src="https://github.com/user-attachments/assets/ad86b65f-93e8-446c-b646-13458698bc84" />

<img width="1280" height="1017" alt="image" src="https://github.com/user-attachments/assets/9dc3036f-671c-43c9-96d6-65667bbe6f3b" />

표시 필터 적용 칸에 "frame contains "recipe.docx" " 를 입력하여서, 전체 패킷 데이터에서 recipe.docx 문자열이 포함되는지 확인하고, 포함하는 것들만 출력되게끔 하였다. 그렇게 해서 출력된 4개 중 2번째 거를 TCP 따라가기로 살펴보니, PK ..  라는 시그니처를 확인할 수 있었다. 이를 16진수 덤프 값으로 변환하여 4바이트만 추출하면 `50 4B 03 04` 가 해당 파일의 매직 넘버인 것을 확인할 수 있다.

<br>

</br>

<img width="1280" height="1158" alt="image" src="https://github.com/user-attachments/assets/1949332c-da5b-41c7-8415-bfd99985bd95" />

<img width="1280" height="994" alt="image" src="https://github.com/user-attachments/assets/5ee57ab1-b856-4b6b-ad3a-3782c3d3c493" />

<img width="1280" height="920" alt="image" src="https://github.com/user-attachments/assets/fd58d8ff-c8cf-4d3d-bf16-deade2d4b1ad" />

<img width="1280" height="1147" alt="image" src="https://github.com/user-attachments/assets/ed6ae9d6-9974-496f-ac87-b15dd77089b3" />

위에서 발견한 부분을 recipe로 따로 추출하고, 이를 HxD에서 PK 시그니처 앞에 쓸데없는 부분을 삭제시킨 후, 이를 다시 recipe.docx로 저장하였다. 이를 다시 HxD에서 실행하고, 체크섬을 확인해보니, MD-5  체크섬

`8350582774E1D4DBE1D61D64C89E0EA1` 가 해당 파일의 MD5 체크섬인 것을 확인할 수 있다.

<br>

</br>

<img width="808" height="316" alt="image" src="https://github.com/user-attachments/assets/9c60452e-587c-4ad8-8797-8becaf3e7c0f" />
 

그렇게 추출한 docx 파일을 열어보니 다음과 같은 래시피를 발견할 수 있었다.

<br>

</br>
<br>

</br>

FLAG ▷▶

```
1. Sec558user1
2. Here's the secret recipe. I just download it from the file server. 
   Just copy to a thumb drive and you're good to go.
3. recipe.docx
4. 50 4B 03 04
5. 8350582774E1D4DBE1D61D64C89E0EA1

```
