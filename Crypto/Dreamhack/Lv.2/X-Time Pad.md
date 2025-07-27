<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "X-Time Pad"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.07.27 풀이

<img width="614" height="1088" alt="image" src="https://github.com/user-attachments/assets/a1b70ddc-8452-4770-b336-fa45060bc5b3" />

해당 문제를 풀어보려고 한다.

<br>

</br>

```
#!/usr/bin/env python3
import sys

#flag xor key
def flag_enc():
    with open('./flag', 'r') as f:
        flag = f.read()[3:-1]
        list = ['0b']
        for ch in flag:
            list.append(format(ord(ch), 'b').zfill(8))
        binf = "".join(list)
    with open('./key', 'r') as f:
        key = f.read()

    flag_enc = bin(int(binf, 2) ^ int(key, 2))
    print(f'flag_enc: {flag_enc}\n')

    return key

def key_gen(key):
    new_key = bin(int(key,2) ^ int('0b10010110101011100100111011100101101011110011001110000101111010111110010111100000111110000000010101101011001100010100010101111000111111100010001010110000010111110111110010001111110011110101001011111010100101010100001110010111111010001101111110011001010110011001010101010000001010100000101101001010010010100010100001011101011011010011010101111111010010100111011001100000101011100001010111111101000110011000110101111111010111001101111110011101101100011101001111111000010011010111100010111001100101011111101111111001',2))
    return new_key


def input_enc(key):
    p = input("Plain text : ")
    if (len(p) > 64) :
        print('Max length: 64')
    else :
        plist = ['0b']
        for ch in p:
            plist.append(format(ord(ch), 'b').zfill(8))
        binp = "".join(plist)
        key = key[0:len(binp)]

        input_enc = int(binp, 2) ^ int(key, 2)
        print(f'input_enc: {input_enc}\n')
        
        sys.exit()

def main():
    o_key = flag_enc()
    n_key = key_gen(o_key)
    input_enc(n_key)


if __name__ == '__main__':
    main()
```

다음과 같은 py 파일이 문제 파일로 제공되었다. 

다음 코드는 XOR 기반의 암호화 구조를 사용하는데, flag_enc() 함수는 ./flag 파일의 내용을 바이너리로 변환한 후, ./key 파일의 바이너리 키와 XOR 하여 암호화된 flag_enc를 출력하는 것으로 보인다. 

key_gen() 함수는 기존 키와 고정된 랜덤 비트 시퀀스를 XOR하여 새로운 키를 생성하고, input_enc() 함수는 사용자의 입력를 새롭게 생성된 키와 XOR하여 암호화된 input_enc를 출력하는 것으로 보인다. 

서버에 요청을 보내서, 서로 다른 input_enc 값을 수집하고, 각 input_enc의 인덱스별 최소값을 저장한 후에, 역연산을 활용하여, `flag_enc[i] ^ min(input_enc[i]) = flag[i]` 알고리즘으로 계산하는 코드를 제작하면 될 것 같다.
<br>

</br>

```
from pwn import *

# ====== 설정 ======
HOST = 'host8.dreamhack.games'
PORT = 20742
PLAINTEXT = '1' * 64  # 입력값은 길이 64 고정
RAND_BIT = int(
    '10010110101011100100111011100101101011110011001110000101111010111110010111100000111110000000010101101011001100010100010101111000111111100010001010110000010111110111110010001111110011110101001011111010100101010100001110010111111010001101111110011001010110011001010101010000001010100000101101001010010010100010100001011101011011010011010101111111010010100111011001100000101011100001010111111101000110011000110101111111010111001101111110011101101100011101001111111000010011010111100010111001100101011111101111111001',
    2
)

conn = remote(HOST, PORT)

# flag_enc 수신
line = conn.recvline().decode().strip()
assert line.startswith('flag_enc:')
flag_enc_str = line.split('flag_enc: ')[1]
flag_enc = int(flag_enc_str, 2)

# input 전송
conn.recvuntil(b'Plain text : ')
conn.sendline(PLAINTEXT.encode())

# input_enc 수신
line = conn.recvline().decode().strip()
assert line.startswith('input_enc:')
input_enc = int(line.split('input_enc: ')[1])

conn.close()

# 평문 → 바이너리 → 정수
bin_input = ''.join(format(ord(c), '08b') for c in PLAINTEXT)
input_int = int(bin_input, 2)

# new_key = input XOR input_enc
new_key = input_int ^ input_enc

# old_key = new_key XOR rand_bit
old_key = new_key ^ RAND_BIT

# flag = old_key XOR flag_enc
flag_int = old_key ^ flag_enc
flag_bin = bin(flag_int)[2:]

# 정렬
if len(flag_bin) % 8 != 0:
    flag_bin = flag_bin.zfill(len(flag_bin) + (8 - len(flag_bin) % 8))

# 8비트 단위로 변환
flag_bytes = bytearray()
for i in range(0, len(flag_bin), 8):
    flag_bytes.append(int(flag_bin[i:i+8], 2))

# 출력
try:
    print(flag_bytes.decode("utf-8"))
except:
    print(flag_bytes.decode("utf-8", errors="ignore"))
```

Dreamhack 서버에 접속해 암호화된 flag_enc와 input_enc 값을 받아온 뒤,
입력값 '1'*64를 기준으로 XOR 연산을 역으로 수행해 원래의 flag를 복호화하는 스크립트를 작성해보았다.

<br>

</br>

<img width="580" height="122" alt="image" src="https://github.com/user-attachments/assets/324dd411-56a7-49d6-9d8e-ff4fe8550836" />

이렇게 답을 얻을 수 있었다.

<br>

</br>

```
DH{94fa53454c853ace51d618f4dbff73971d07d6292e376cdbdcd93a90cbceb0d1}
```
