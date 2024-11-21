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

