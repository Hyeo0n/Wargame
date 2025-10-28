<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "secret message"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.10.28 풀이

<img width="596" height="1070" alt="image" src="https://github.com/user-attachments/assets/295f1120-c378-4bda-94cc-ac67b1ee4b3a" />

다음 문제를 풀이해보도록 하겠다.
<br>

<img width="1156" height="596" alt="image" src="https://github.com/user-attachments/assets/510c8484-73a5-408c-b79d-9009d7211654" />

문제 파일 `prob`을 IDA로 분석해보았다. 다음과 같이 main을 살펴보니,
secretMessage.raw 파일을 읽어서 secretMessage.enc(주어진 문제 파일)에 sub_7FA 함수에 따라 변환된 것을 적용해 주고, 원본 raw 파일을 지운 뒤 done!을 찍고 종료하는 함수로 보인다. 
<br>

```
__int64 __fastcall sub_7FA(FILE *a1, FILE *a2)
{
  unsigned __int8 v3; // [rsp+17h] [rbp-9h]
  int c; // [rsp+18h] [rbp-8h]
  int v5; // [rsp+1Ch] [rbp-4h]

  if ( a1 && a2 )
  {
    v5 = -1;
    v3 = 0;
    while ( 1 )
    {
      c = fgetc(a1);
      if ( c == -1 )
        return 0LL;
      fputc(c, a2);
      if ( c == v5 )
      {
        v3 = 0;
        while ( 1 )
        {
          c = fgetc(a1);
          if ( c == -1 )
            break;
          if ( c != v5 )
          {
            fputc(v3, a2);
            fputc(c, a2);
            v5 = c;
            break;
          }
          if ( ++v3 == 0xFF )
          {
            fputc(255, a2);
            v5 = -1;
            break;
          }
        }
      }
      else
      {
        v5 = c;
      }
      if ( c == -1 )
      {
        fputc(v3, a2);
        return 0LL;
      }
    }
  }
  else
  {
    *__errno_location() = 2;
    return 0xFFFFFFFFLL;
  }
}
```
이어서 sub_7F4 함수를 살펴보았다. 
코드를 살펴보니, 항상 한 바이트를 읽고, 이 읽은 값이 이전 바이트와 같다면 연속 동일 바이트 처리를 한다. 
즉, raw에서 읽은 값을 enc에 저장하고 이전의 값과 동일한 값이면 동일한 갯수를 쓰고, 해당 값을 한 번 더 써서 마무리한다고 한다. 이때 갯수의 최대값은 255이고, 255의 경우에는 문자를 한 번 더 쓰지 않는다고 한다.

예를 들자면 원본 데이터가 A A A B B C 라면, 맨 처음 A는 그대로 A로 출력하고, 다음 A를 읽을 때는 반복되기 떄문에 while문으로 진입하고, 세 번째 A를 읽어들이면 추가로 한 개 더 나왔다는 뜻으로 v3 =1이 된다. 그 후, B를 읽어들이므로 연속이 끝나니 v3은 그대로 1이 되어, 이떄 출력은 A A 01 B가 된다. 그 후 또 B가 나온 후 다시 루프로 진입하니, C가 나와 연속이 끝나므로 v3=0. 출력은 A A 01 B B 00 C 로 된다.
<br>

```
import io

def decode(a1, a2):
    prev = None  # 직전 바이트 (bytes 또는 int로 관리)
    while True:
        c = a1.read(1)
        if not c:
            break

        a2.write(c)
        b = c[0]                   # int(0..255)

        if prev is not None and b == prev:
            
            cnt_b = a1.read(1)
            if not cnt_b:
                
                break
            count = cnt_b[0]

            if count != 0xFF:
                
                nxt = a1.read(1)
                
                if not nxt:
                    if count:
                        a2.write(bytes([b]) * count)
                    break

                
                if count:
                    a2.write(bytes([b]) * count)
                a2.write(nxt)

                prev = nxt[0]      
            else:
                
                a2.write(bytes([b]) * 255)
                prev = None        
        else:
            prev = b

def main():
    with open('secretMessage.enc', 'rb') as a1, open('secretMessage.raw', 'wb') as a2:
        decode(a1, a2)

if __name__ == '__main__':
    main()
```

다음 원리를 활용하여, 다음과 같이 secretMessage.raw 파일을 만들어주는 exploit.py 코드를 작성해보았다.
이를 실행하면, secretMessage.raw 파일이 생성되는데, 이를 다시 
<br>

<img width="562" height="86" alt="image" src="https://github.com/user-attachments/assets/c7547e39-b0d6-412f-8031-68ac66a39f9a" />
<img width="1466" height="840" alt="image" src="https://github.com/user-attachments/assets/99943032-d33c-4d8e-8ec7-6ef7789b92a2" />

해보면, 플래그 값을 얻을 수 있다.
<br>

```
DH{93589e6c1db065fa95075ab5e3790bc1}
```

