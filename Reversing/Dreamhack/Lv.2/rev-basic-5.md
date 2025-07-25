<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "rev-basic-5"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.07.25 풀이

<img width="646" height="1306" alt="image" src="https://github.com/user-attachments/assets/f5614e10-8cdb-4c11-bb8c-202eee2e7232" />

<img width="1620" height="548" alt="image" src="https://github.com/user-attachments/assets/3e944768-b1c0-4a68-be16-840707016101" />

문제 파일을 받고, 그 안에 exe 파일이 있어서 이를 실행해보니, 다음과 같은 화면이 나온다. input 뒤에 특정한 것을 입력해서 통과되면, 플래그 값을 얻을 수 있을 것으로 보인다.

<br>

</br>

<img width="1862" height="470" alt="image" src="https://github.com/user-attachments/assets/3bfaa025-b26a-4549-8f81-07c5de1408d4" />

x64dbg의 문자열 참조 찾기에서 correct을 입력해서, 해당 부분을 살펴보았다. 
사용자가 입력하고, 이 입력값을 chall5.7FF78EF81000 함수에서 비교하고, correct이나 wrong 메시지를 출력하는 것으로 보인다. 

<br>

</br>

<img width="1766" height="698" alt="image" src="https://github.com/user-attachments/assets/49345a37-2556-4c68-b935-090150c6f01b" />

해당 함수를 보니, flag 검증 루프로 이루어져 있다. 입력 버퍼(input[0]~input[23])를 순회하면서, input[i] + input[i+1] == 정답배열[i]를 만족해야 통과하는 루프인 것으로 보인다.
한 번이라도 불일치하면 실패이고, 모두 만족해야 성공인 것으로 보인다. 정답 베열 주소는 7FF78EF83000로, 이와 입력값을 비교하는 것으로 보인다.

<br>

</br>

<img width="1276" height="146" alt="image" src="https://github.com/user-attachments/assets/9faaeea9-a8ca-4ecf-9163-79ff328bc095" />

7FF78EF83000 주소에 저장된 값이다.

<br>

</br>


```
byte_arr = [
  0xAD, 0xD8, 0xCB, 0xCB, 0x9D, 0x97,
  0xCB, 0xC4, 0x92, 0xA1, 0xD2, 0xD7,
  0xD2, 0xD6, 0xA8, 0xA5, 0xDC, 0xC7,
  0xAD, 0xA3, 0xA1, 0x98, 0x4C, 0x00
]

input_vals = [0] * 24
input_vals[23] = 0  # 마지막은 null (0)

# 뒤에서부터 하나씩 역산
for i in range(22, -1, -1):
    input_vals[i] = byte_arr[i] - input_vals[i + 1]

# 각 값을 문자로 변환
flag = ''.join(chr(b) for b in input_vals)
print(flag)
```

아까 위에서 본 계산을 역산하여 입력값을 계산하는 코드를 작성하였다. input[i] = byte[i] - input[i + 1] 역산 방식으로 하나씩 역산하는 코드이다.

<br>

</br>

<img width="812" height="112" alt="image" src="https://github.com/user-attachments/assets/377e29fa-f4e0-4f85-9928-4756803dd5ea" />

해당 스크립트를 실행해보니, flag값을 얻을 수 있었다.
<br>

</br>

```
DH{All_l1fe_3nds_w1th_NULL}
```
