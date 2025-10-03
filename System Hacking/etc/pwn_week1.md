<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> "pwn_week1.zip"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.09.14 풀이

<img width="1270" height="1094" alt="image" src="https://github.com/user-attachments/assets/5a03db3f-56a7-4da1-be22-bb2d8580a1c3" />

해당 week1.c를 살펴보니, 랜덤한 덧셈 문제 20개를 낸 후,

사용자가 매번 답을 입력해야 하고, 하나라도 틀리면 Wrong answer! 문자열을

출력 후 종료되는 코드이다. 

20개 문제를 전부 맞추면 마지막에 Success! Easy~~를 출력하는 코드이다.

<br>

</br> 

```
from pwn import *
import sys, os

context.clear(arch='amd64', os='linux')
context.log_level = 'info'

assert sys.stdout.isatty(), "터미널에서 실행하세요 (python solve.py)."

p = process("./week1", stdin=PTY, stdout=PTY)

for _ in range(20):
    line = p.recvuntil(b"=")           
    parts = line.decode().replace(".", "").split()
    a = int(parts[1]); b = int(parts[3])
    p.sendline(str(a + b).encode())    

p.interactive()
```

다음과 같이 풀이 파이썬 코드를 작성하였다.

from pwn import *를 통해 pwntools을 임포트해주고,

context.log_level = 'info' 를 통해 pwntools 로그를 info로 보여주도록 해서,

제시 사진과 같은 "Switching to interactive mode" 같은 안내가 출력되도록 한다.

그 후, ./week1 바이너리를 자식 프로세스로 실행하고, 

20개의 덧셈 문제를 내부 버퍼로만 읽게 하여, 화면에 출력되지 않게 한다.

주어진 덧셈 문제에서 피연산자를 추출하여, 정답을 전송하는 루프를 20번 반복한다.

그 후,  p.interactive() 를 통해, 터미널에 연결하는 코드를 작성해보았다.  

<br>

</br> 

<img width="1132" height="256" alt="image" src="https://github.com/user-attachments/assets/ea122e94-b263-4004-9de1-f2ed160e4862" />

위에서 작성한 파이썬 코드를 실행하니, 다음과 같이 문제를 해결할 수 있었다.
      
