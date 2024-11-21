<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "just read memory"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.11.21 풀이


![image](https://github.com/user-attachments/assets/00f89d3c-bcf7-4c2d-af6e-03aa526f8c3e)

![image](https://github.com/user-attachments/assets/5e8f2a72-b6a6-4749-b47b-0cee0e94a811)

문제 파일을 다운받아보니, main 이라는 파일만 있길래 살펴보니, 파일이 잘 열리지도 않고, 

메모장으로 열어보니 알 수 없는 데이터들이 나열되어 있다..
<br>

</br> 

![image](https://github.com/user-attachments/assets/0383f826-c6c5-4ead-95f8-9328d3a34ce2)

이 데이터들을 식별하기 위해, IDA 를 사용하여서, 해당 파일을 살펴보았다. 

아이다로 살펴보니, 어셈블리어 코드로 구성된 흐름도가 나타났다. 

f5 키를 눌러 이를 C 언어로 변환해보니,

![image](https://github.com/user-attachments/assets/dd0ab002-b96a-47d6-96ce-f37a1d8d3328)

![image](https://github.com/user-attachments/assets/6d42d02b-a157-4722-aa08-c613fdf09a95)

위와 같은 main 함수 코드가 출력된다. 

해당 코드를 살펴보니, 사용자 입력을 받아 특정 조건을 확인하고, 조건에 따라 Correct 메시지와 플래그를 출력하거나

Wrong 메시지를 출력하는 프로그램으로 보인다. 

<br>

</br> 

`__isoc99_scanf("%64s", v4);` 부분을 통해, 사용자에게 최대 64바이트 길이의 문자열을 입력받고, 

그 뒤에, `head_node = malloc(0x10uLL);` 부분을 통해, 첫 번쨰 노드를 메모리에 할당하는 것으로 보인다. 

이 뒤에 코드들을 보니, 데이터를 담고 있는 노드들이 포인터를 통해 서로 연결되어 있는 구조인 연결 리스트를 
초기화하고 있는 것으로 보인다.

`for ( i = 0; i <= 63; ++i ) 
    append_list((unsigned int)i);` 부분을 통해, 연결 리스트에 숫자 0부터 63까지를 노드에 추가하여, 문자열을 만드는 것으로 보인다. 
    
    
  아마 이 부분이 나중에 플래그가 되지 않을까 생각해본다.. 

  그 뒤에, `while ( v8 )
{
    if ( *((unsigned __int8 *)v4 + v9) != *v8 )
        v10 = 0;
    v8 = (char *)*((_QWORD *)v8 + 1);
    ++v9;
}` 부분을 통해, 사용자가 입력한 값과 위에 for문에서 저장된 연결 리스트 노드의 데이터를 

각각 하나씩 비교하는 것으로 보인다.

그리고, `while ( head_node )
{
    *(_BYTE *)head_node = 0;
    v6 = (void *)*((_QWORD *)head_node + 1);
    *((_QWORD *)head_node + 1) = 0LL;
    free(head_node);
    head_node = v6;
}` 부분은 비교 판정을 끝낸 후, 위에서 만든 연결 리스트를 돌아가면서 각 노드를 메모리에서 해제하고 있다. 

최종적으로, `if ( v10 )
{
    puts("Correct");
    printf("flag is DH{");
    for ( j = 0; j <= 63; ++j )
        printf("%d", *((_BYTE *)v4 + j) % 0xAu);
    puts("}");
}
else
{
    puts("Wrong");
}` 부분을 통해, 사용자가 입력한 각 문자의 값을 10으로 나눈 나머지를 순차적으로 계산하여서 flag 값으로 출력하는 것으로 보인다.

그래서, 이 문자열을 알아내는 방식으로 문제 풀이를 진행해야 될 것 같다.

하지만, 현실적으로 64자리의 랜덤된 문자열을 알아낼 방법은 딱히 없어 보이고, 

프로그램을 조금 조작을 하여서, 입력된 값을 무조건 맞는 값으로 처리하게 만들어야 할 것으로 보인다.  

<br>

</br> 

![image](https://github.com/user-attachments/assets/e7d165fe-d344-4f6a-b58c-dca6ab528b71)

코드를 살펴보니, 사용자 입력 값 (v4)과 연결 리스트에서 읽어온 문자열을 비교하는 부분을 보니, 
<br>

</br> 

```
mov eax, [rbp+var_C]  ; v9 (현재 비교 중인 위치)
cdqe                  ; 확장 (32비트를 64비트로 변환)
movzx eax, byte ptr [rbp+rax+var_70]  ; 사용자 입력의 현재 문자
mov edx, al           ; edx에 사용자 입력 문자 저장
mov rax, [rbp+var_18] ; 연결 리스트 노드
movzx eax, byte ptr [rax] ; 연결 리스트 노드의 문자
cmp edx, eax          ; 사용자 입력 문자와 목표 문자열 문자 비교
jz  short loc_12DE    ; 같으면 loc_12DE로 이동

```

`mov rax, [rbp+var_18] ; 연결 리스트 노드`, `movzx eax, byte ptr [rax] ; 연결 리스트 노드의 문자
` 부분을 통해, 연결 리스트의 각 노드들의 데이터를 불러오는 것을 확인할 수 있었다.
그 후, `cmp edx, eax` 부분을 통해, 각 데이터들을 비교하면서 검증하는 것으로 보인다.
<br>

</br> 

![image](https://github.com/user-attachments/assets/6c22a74b-f2ca-43b6-b8dd-a8d0700fad97)

위에서 비교 검증을 거친 후, 모든 데이터가 일치하면, `jz short loc_12DE`를 통해, loc_12DE로 이동하는 것으로 보인다.

<br>

</br> 

일단 연결 리스트 데이터 값들을 불러오게 한 후에 뒤에 있는
비교하는 cmp 부분을 지우고, 

```
lea rdi, [rbx+rax+var_70]  ; 사용자 입력 위치의 주소를 rdi에 저장
mov [rdi], al              ; 목표 문자열 문자를 사용자 입력에 복사
jz  short loc_12DE
```

부분으로 수정해보려고 한다. `lea rdi`를 통해 사용자 입력 위치의 주소를 rdi 레지스터에 저장해놓고,

`mov [rdi], al`를 통해 불러온 연결 리스트에 값을 rdi, 즉 사용자 입력 위치에다가 저장하게끔 수정해보았다.

그 후, loc_12DE로 이동하는 부분을  `jz` 로 바꾸어 항상 loc_12DE로 이동하게끔 수정한다.

<br>

</br> 

![image](https://github.com/user-attachments/assets/1a1e3d71-e487-4bb6-9ff5-c5e56299de48)
 
<br>

</br> 

![image](https://github.com/user-attachments/assets/7763759c-a413-4ef2-91c0-e76a91c3c3a1)

현재 IDA Freeware를 사용하고 있기 떄문에, Hxd에서 따로 해당 hex 값을 직접 수정해주었다.

<br>

</br>

![image](https://github.com/user-attachments/assets/8505c2aa-3de3-4e07-bd86-dc93c48a8b36)

이를 kali에서 실행시켜보니, 아무거나 입력값을 넣어도 Correct이 출력되면서 플래그 값을 얻을 수 있었다!

<br>

</br>

```
DH{7078981632989894389654921898749418541874549056189474343058581690}
```
<br>

</br>

![image](https://github.com/user-attachments/assets/731fc951-a78e-4bc4-bbde-dd3974d65e18)

