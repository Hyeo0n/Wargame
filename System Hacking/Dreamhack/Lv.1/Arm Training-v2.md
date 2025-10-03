<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "Arm Training-v2"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.07.11 풀이

<img width="498" height="576" alt="image" src="https://github.com/user-attachments/assets/eba6525a-d2b7-4861-937c-1dc13caa33fd" />

해당 문제를 풀이해보려고 한다.

<br>

</br> 

<img width="700" height="502" alt="image" src="https://github.com/user-attachments/assets/f52e29a5-be93-40c4-9736-ae4a772477fe" />

<img width="1280" height="711" alt="image" src="https://github.com/user-attachments/assets/ad4ea1ac-3f83-41af-9481-d8386be9894f" />

Ghidra로 main 함수를 살펴보았다.

살펴보니 20바이트 버퍼에 200바이트 입력하는 것을 보아 스택 오버플로우가 발생하는 것을 알 수 있다.

어셈블리를 살펴보니, FP(r11)로부터 0x18 떨어진 위치에 리턴 주소가 있는 것을 알 수 있다.

익스플로잇 코드 작성 시, 페이로드를 0x18 바이트 패딩 + 리턴 주소로 덮으면 될 것으로 보인다.

<br>

</br> 

<img width="638" height="468" alt="image" src="https://github.com/user-attachments/assets/dbc05c91-1708-4036-8612-082d9efb63cb" />

<img width="708" height="466" alt="image" src="https://github.com/user-attachments/assets/b6048ae6-2a88-456c-950f-d244921e0fa1" />


v2에는 shell 함수가 없고, gadget1, 2 함수가 있다. 

이들을 살펴보니, system("/bin/sh") 직접 호출하려면, r0 레지스터에 "/bin/sh" 주소를 넣고, system() 함수를 호출해야 하는 것을 알 수 있다.

<br>

</br> 

<img width="1280" height="560" alt="image" src="https://github.com/user-attachments/assets/e894b800-b238-4318-8944-7253140159c0" />

<img width="1280" height="535" alt="image" src="https://github.com/user-attachments/assets/22a4c6cf-d23b-4a10-9505-1132889194e6" />

버퍼 오버플로우로 return 주소 → pop {r3, pc} →  r3 = "/bin/sh" → pc = gadget1

→ gadget1 내부: r0 ← r3 → system(r0) →  r0 = "/bin/sh" →  system("/bin/sh") 호출 → 쉘 획득

하는 흐름으로 접근해보려고 한다.

<br>

</br> 

<img width="654" height="500" alt="image" src="https://github.com/user-attachments/assets/323e2e52-e9dc-4e8f-aac9-104ba925da2c" />

<img width="830" height="200" alt="image" src="https://github.com/user-attachments/assets/94b971a8-d673-4bd3-98e4-b130280c7efb" />

다음과 같이 익스플로잇 코드를 작성하고, 이를 실행시키니 플래그 값을 얻을 수 있었다.

<br>

</br> 

```
DH{49AD4F9C3D6B72A8E5DE3D71EB435E1791041BCB130939DA82912F0423001CF2}
```
