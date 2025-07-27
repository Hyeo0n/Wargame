<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "Simple Crack Me 2"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.07.27 풀이

<img width="608" height="1322" alt="image" src="https://github.com/user-attachments/assets/c98e8e4d-6adc-41cb-8f02-9631217a2c3f" />

해당 문제를 풀어보려고 한다.

<br>

</br>

<img width="992" height="170" alt="image" src="https://github.com/user-attachments/assets/06edd477-abf5-465c-a63b-e36c0cae457f" />

해당 파일을 실행해보니, 입력값을 입력받고 wrong 메시지가 출력된다. 

<br>

</br>

<img width="1114" height="1146" alt="image" src="https://github.com/user-attachments/assets/f819872b-6ce5-4fd9-834f-e66e533705cb" />

이 파일을 IDA로 살펴보았다. main을 살펴보니, 사용자 입력을 받고, 입력길이가 32인지 확인한 후, 여러 변형 함수들이 호출되어 실행되고, 이를 s1과 s2를 비교하여, correct/wrong 메세지를 출력하는 것으로 보인다. 

<br>

</br>

<img width="890" height="960" alt="image" src="https://github.com/user-attachments/assets/6579af98-69b8-4298-8195-c8bbba47f759" />

<img width="1062" height="976" alt="image" src="https://github.com/user-attachments/assets/b2261456-6c57-4ad9-bce7-510251080193" />

<img width="788" height="584" alt="image" src="https://github.com/user-attachments/assets/8a7fb17f-3823-446d-b389-ae877e644811" />

main 함수에 나온 여러 변형 함수들을 살펴보았다. 이를 살펴보니, sub_4011EF는 s1의 각 바이트를 key와 XOR 연산하는 것으로 보인다.

sub_401263 함수는 2번쨰 인자인 a2만큼 s1의 모든 바이트를 증가시키는 것으로 보인다. 

sub_4012B0 함수는 s1[i] = s1[i] - a2 와 같은 단순 덧셈/뺄셈 연산을 진행하는 것으로 보인다. 

<br>

</br>

<img width="1530" height="906" alt="image" src="https://github.com/user-attachments/assets/f6ab0aa3-51d1-4e0b-b989-34d3452a13b1" />

위 변형 함수에서 사용되는 key 배열을 살펴보았다. 

unk_402068에서는 [0xDE, 0xAD, 0xBE, 0xEF], unk_40206D에서는 [0xEF, 0xBE, 0xAD, 0xDE], unk_402072에서는 [0x11, 0x33, 0x55, 0x77, 0x99, 0xBB, 0xDD] 를 얻을 수 있었다.

<br>

</br>

```
def xor_with_param2(data, key):
    return [(b ^ key[i % len(key)]) & 0xff for i, b in enumerate(data)]

def inc_with_param2(data, num):
    return [(b + num) & 0xff for b in data]

def dec_with_param2(data, num):
    return [(b - num) & 0xff for b in data]

def decode(input_bytes):
    step1 = xor_with_param2(input_bytes, DAT_00402072)
    step2 = inc_with_param2(step1, 13)            # DEC -13 == INC 13
    step3 = inc_with_param2(step2, 77)
    step4 = xor_with_param2(step3, DAT_0040206d)
    step5 = inc_with_param2(step4, 90)
    step6 = dec_with_param2(step5, 31)
    step7 = xor_with_param2(step6, DAT_00402068)
    return step7

# Key arrays
DAT_00402068 = [0xde, 0xad, 0xbe, 0xef]
DAT_0040206d = [0xef, 0xbe, 0xad, 0xde]
DAT_00402072 = [0x11, 0x33, 0x55, 0x77, 0x99, 0xbb, 0xdd]

# Final encoded result (s2)
PTR_DAT_00404050 = [
  0xf8, 0xe0, 0xe6, 0x9e, 0x7f, 0x32, 0x68, 0x31,
  0x05, 0xdc, 0xa1, 0xaa, 0xaa, 0x09, 0xb3, 0xd8,
  0x41, 0xf0, 0x36, 0x8c, 0xce, 0xc7, 0xac, 0x66,
  0x91, 0x4c, 0x32, 0xff, 0x05, 0xe0, 0xd9, 0x91
]

# 복호화
decoded_bytes = decode(PTR_DAT_00404050)

# 출력 (문자열로)
flag = ''.join(chr(b) for b in decoded_bytes)
print(flag)
```

다음과 같이 IDA에서 살펴보았던 연산들을 역산하는 코드를 작성해보았다.

<br>

</br>

<img width="902" height="128" alt="image" src="https://github.com/user-attachments/assets/a2b3255a-d29f-45a8-828c-361abad391e7" />

이렇게 플래그 값을 얻을 수 있었다.

<br>

</br>

```
DH{9ce745c0d5faaf29b7aecd1a4a72bc86}
```
