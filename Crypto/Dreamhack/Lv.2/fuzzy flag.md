<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "fuzzy flag"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.07.25 풀이

<img width="646" height="1000" alt="image" src="https://github.com/user-attachments/assets/9892a894-027d-4728-8b33-d8ad07c66249" />


<img width="1132" height="576" alt="image" src="https://github.com/user-attachments/assets/eb6c2ad9-7af6-4bdd-9246-cb09360daeb7" />

문제파일에 있는 prob.py을 살펴보았다. flag 파일을 바이너리로 읽고, string.ascii_letters(abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ) 인 총 52개이니 0 이상 52 미만의 난수를 생성하는 것으로 보인다.

즉, 각 flag 바이트에 0~51 중 하나를 더한 리스트를 출력하는 구조로 보인다.

randbelow()는 매번 다른 값을 생성하므로, 이 과정을 여러 번 방복해서 가장 작은 값만 남기면 원래 값인 c를 얻을 수 있을 것으로 보인다.

<br>

</br>
<br>

</br>

```
from pwn import remote

FLAG_LEN = 185
REPEAT = 216
HOST, PORT = "host8.dreamhack.games", 20841

min_vals = [255] * FLAG_LEN  # 초기값 255로 설정

for count in range(REPEAT):
    with remote(HOST, PORT) as p:
        # 정수 리스트로 파싱
        line = p.recvline().decode()
        values = list(map(int, line.strip()[1:-1].split(", ")))

        # 각 인덱스별 최소값 갱신
        min_vals = [min(a, b) for a, b in zip(min_vals, values)]

    print(f"[{count:03}] current min: {min_vals}")

# 최종 결과 출력
print("\n[+] Flag:", bytes(min_vals).decode())
```
<br>

</br>

다음과 같이 스크립트를 작성하였다. 일단 flag 파일은 총 185 바이트이고, 모든 값을 255로 초기화한다.
그 후, Dreamhack 서버에 접속하여, 반복 요청을 한다. 그래서 fuzzy 배열을 수신한다.
문자열에서 괄호 제거하고, 쉼표 기준으로 split 후 int로 변환한다.
그 후, zip()을 이용하여 기존 min_vals와 새로 받은 values를 짝지어, 각각의 위치에 더 작은 값을 저장하여, 난수의 방해를 제거한다.
그리고 현재까지 누적된 최고값 리스트를 bytes로 변환하여, ASCII로 해석시켜, YISF{...} 형태의 플래그를 출력하게끔하는 코드를 작성하였다. 

<br>

</br>

<img width="2704" height="1074" alt="image" src="https://github.com/user-attachments/assets/3ee929b8-5f9a-447e-a67c-124525d3c1d0" />
<img width="2710" height="398" alt="image" src="https://github.com/user-attachments/assets/3e7d84d4-c8e9-45d1-88ba-c32ce8e71bfa" />

해당 스크립트를 실행해보니, 플래그 값을 얻을 수 있었다. 

<br>

</br>

```
YISF{my_two_s0luti0ns:_0ne_where_the_average_0f_rand0m_numbers_remains_constant,_and_an0ther_where_the_upper_and_1ower_bounds_of_random_numbers_remain_c0nstant.What_method_d1d_you_use?}
```
