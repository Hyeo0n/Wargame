<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "Arm Training-v1"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.07.11 풀이

<img width="522" height="1038" alt="image" src="https://github.com/user-attachments/assets/41af38ff-1c45-4591-831c-883f4624514e" />

해당 드림핵 문제를 풀이해보겠다.

<br>

</br> 

<img width="1280" height="776" alt="image" src="https://github.com/user-attachments/assets/aac270ec-3871-438a-911f-0f5453f508ad" />

Ghidra로 해당 바이너리 파일을 살펴보려고 한다.

<br>

</br> 

<img width="756" height="480" alt="image" src="https://github.com/user-attachments/assets/1af0c26f-3c8f-47f8-99f6-bfd020cac3dc" />

main 함수를 살펴보니, 스택에 20바이트짜리 버퍼를 선언했는데,

read(0, auStack_1c, 200)로 200바이트를 입력받게 설정되어있다.

이를 통해 스택 버퍼 오버플로우라는 걸 바로 알 수 있다.

오버플로우를 통해, main 함수의 리턴 주소를 덮을 수 있다는 것을 알 수 있다,

<br>

</br> 

<img width="858" height="522" alt="image" src="https://github.com/user-attachments/assets/9bd43990-4cfe-47b1-961b-22d4c3ce960d" />

shell 함수를 살펴보면, 내부에서 system("/bin/sh") 호출하는 것으로 보인다.

이 함수로 흐름만 옮기면 바로 쉘 획득이 가능할 것으로 보인다, 

해당 shell 함수의 진입 주소는  0x10560이다.

<br>

</br> 

<img width="1280" height="627" alt="image" src="https://github.com/user-attachments/assets/684e17ac-66db-4ec5-9b4e-4cf4c54977a6" />

main 함수를 어셈블리로 살펴보면,

sub r3, r11, #0x18 대목을 통해, 버퍼가 FP 기준 0x18 위치에 있음을 알 수 있다.

따라서, 버퍼 시작부터 정확히 0x18 바이트가 지나면 리턴 주소의 위치인 것을 알 수 있다. 

0x18 바이트 패딩 + 원하는 리턴 주소(shell 주소) 구조로 페이로드 구성하면 될 것으로 보인다.

<br>

</br> 

<img width="694" height="464" alt="image" src="https://github.com/user-attachments/assets/b4c89626-5025-499a-ae2c-259b3b49b725" />

따라서 다음과 같은 익스플로잇 코드를 작성하였다.

<br>

</br> 

<img width="846" height="190" alt="image" src="https://github.com/user-attachments/assets/e1582dc8-c3b8-49c1-81c5-ff5d84cc89c1" />

이를 우분투에서 실행시키니, 플래그 값을 얻을 수 있었다.


<br>

</br> 

```
DH{045F0E06DBEB13E430C6F7076D0F65DACF905015CA592FD7553441DF481ABA65}
```
