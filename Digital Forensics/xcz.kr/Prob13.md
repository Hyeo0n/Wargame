<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> xcz.kr "Prob13"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.04.04 풀이

![image](https://github.com/user-attachments/assets/47acf94f-cf0b-43d2-8077-2c171ec44b67)

키를 찾으면, MD5로 인코딩하고, 이를 소문자로 모두 바꾸면 우리가 찾는 플래그 값인 것으로 보인다.

<br>

</br>

![image](https://github.com/user-attachments/assets/cb62fc0e-a636-4a7c-9d62-835400b046d5)

![image](https://github.com/user-attachments/assets/caf446f5-e5e2-46f7-a286-57688201a28e)

문제 파일을 살펴보니, 확장자를 알 수 없는 파일이 있어, 이를 HxD로 열어보니, 0A 0D 0D 0A 로 시작하는 것을 확인할 수 있었는데, 이를 찾아보니 pcapng 파일의 헤더 시그니처라고 한다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/8e919473-63ef-4e00-b2c1-8291438e4d97)

![image](https://github.com/user-attachments/assets/bdb5a852-1f04-4a77-b5a3-e4f55de9f849)

그래서, 이를 pcapng 확장자를 붙여주고, 와이어샤크로 열어보니, 잘 열리는 것을 확인할 수 있었다.

<br>

</br>

![image](https://github.com/user-attachments/assets/5c312926-de76-4392-a93e-f7a2b523b80a)

가장 기본적인 http 부터 살펴보자 하며, http를 살펴보니, 플래그와 관련있어 보이는 treasure 1, 2, 3과 PNG 관련된 패킷을 살펴볼 수 있었다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/63dfe2ac-74e8-4b71-a7ff-c293d6671bbb)

![image](https://github.com/user-attachments/assets/4885987a-c3fe-41e7-a7d0-9550dee8285b)

![image](https://github.com/user-attachments/assets/9f3dd309-15e6-462b-a0f2-5b96f7f08a34)

해당 PNG 부분 패킷을 살펴보니, PNG 헤더 시그니처와 여러 청크 구조들을 살필 수 있었다. 하지만, IEND인 푸터 시그니처가 없는 것을 확인할 수 있었다.

<br>

</br>

![image](https://github.com/user-attachments/assets/20c14c6c-798f-4e7a-b3f3-c344c8a0aa26)

![image](https://github.com/user-attachments/assets/213db50c-e98c-47c2-98ed-e62ede10ed63)

treasure3 패킷 밑에 해당 treasure3 페이지의 response 패킷 데이터를 살펴보니, IEND를 살펴볼 수 있었다.

이를 통해, treasure 1, 2, 3이 각각 다른 PNG가 아니라 셋이 합쳐져서 하나의 PNG를 이루고 있음을 알 수 있다.

<br>

</br>

![image](https://github.com/user-attachments/assets/6b25d6bf-b510-40a8-8e30-ad027fd98a75)

![image](https://github.com/user-attachments/assets/2e199014-f20e-4a12-b6c4-0a2d7e598143)

![image](https://github.com/user-attachments/assets/470c0243-bbf7-47a8-a079-3101157f2319)

![image](https://github.com/user-attachments/assets/1fab7e6f-4137-4db7-bc74-a0ac544d371e)

HTTP 객체 내보내기를 누르면, HTTP 패킷으로부터 온 데이터를 추출할 수 있는데, 이 곳에서 treasure 1, 2, 3을 추출한다. 그리고, HxD에서 이들 세 개를 합쳐서 재구성한다.

<br>

</br>
<br>

</br>

![image](https://github.com/user-attachments/assets/528efdc3-767c-4a5c-a985-59f7944d51c9)

그러면, 위와 같은 플래그 값이 담긴 이미지를 얻을 수 있다.

<br>

</br>

![image](https://github.com/user-attachments/assets/1d6d46de-a8e3-42b8-85c8-74434a1d288a)

해당 이미지에 있는 값을 MD5 해시값을 변환하면 플래그를 얻을 수 있다!. 

<br>

</br>
<br>

</br>

FLAG ▷▶

```
2f4a11572d9ff67baebdb2f3899d7a84
```












