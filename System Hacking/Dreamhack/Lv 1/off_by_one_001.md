<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "off_by_one_001"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.11.20 풀이

![image](https://github.com/user-attachments/assets/85842427-849c-491d-9cd6-201b04c54379)

문제 설명을 살펴보니, 해당 문제의 바이너리와 소스 코드를 살펴본 후, 파이썬으로 익스플로잇 코드를 작성한 후, 이를 실행시켜,

해당 소스 코드에 있는 get_shell 함수를 실행시킴으로서, flag 파일을 읽어들여 문제를 푸는 것으로 보인다. 

<br>

</br> 

![image](https://github.com/user-attachments/assets/fd668d79-0e51-47f7-8b67-c396f4dceb22)

![image](https://github.com/user-attachments/assets/7dc50c1c-3997-41e5-8d2b-093dd10fcbb8)

C 파일의 코드를 살펴보니, 프로그램이 설정된 시간(30초) 내에 작업을 완료하지 못하면, 호출되어 "TIME OUT" 메시지를 출력하고, 프로그램을 종료하는 것으로 보인다.

또한, initialize 함수를 통해, 입출력 버퍼를 비활성화하고, 30초 흐에 SIGALRM 신호가 발생하도록 설정하는 것으로 보인다.

또한, read_str 함수 부분을 살펴보면, 표준 입력에서 최대 `size` 바이트를 읽고 이를 `ptr`에 저장하는 것으로 보인다. 입력된 길이 `len` 을 출력한 후, 그 위치에 널 문자를 삽입하는 것으로 보인다. 

size 만큼 문자열에 입력을 받고 입력을 받은 길이에 해당하는 인덱스에 "\0"을 넣어주는 것으로 보인다.

하지만, `len`이 `size` 보다 큰 경우, 버퍼에 데이터를 쓰는 소프트웨어가 버퍼의 용량을 초과하여 인접한 메모리 위치를 덮어쓸 떄 발생하는 "버퍼 오버플로우" 문제가 발생할 것으로 보인다. 

문제 설명에서 언급된 get_shell 함수를 살펴보니, 쉘을 실행시키는 것으로 보인다.

main 함수를 살펴보니, age를 입력받고, 입력받은 age가 0이면 get_shell 함수를 실행시켜, 쉘을 띄우는 것으로 보인다. 

<br>

</br> 
즉, flag 값을 얻으려면, age 변수에 0이 들어가게끔 하면 될 것으로 보인다.
하지만, age를 따로 입력받는 값은 없고, 이름만을 입력받는 구성이 있기 때문에, 이 이름을 입력하는 부분을 활용하여, age 값을 0으로 바꿀 수 있는 방법으로 접근해야 될 것 같다. 
또한, 아까 이름을 입력받는 함수인 read_str 함수에서 버퍼 오버플로우 문제에 취약한 구조를 발견하였기 때문에 이를 활용하여 문제를 풀어봐야겠다고 생각했다.

<br>

</br> 

코드를 살펴보면, age 변수가 name 변수보다 먼저 선언된 것을 확인할 수 있다.
일반적으로 지역 변수들은 코드에서 선언된 순서대로 메모리에서 순차적으로 배치된다. 
그러니, 메모리 상에서 name 버퍼가 먼저 할당되고, 그 위에 age 변수가 할당되기 때문에 read_str 함수에서 버퍼 오버플로우 문제를 일부러 일으켜서, name의 범위를 넘어가게 해서, age의 메모리를 덮어쓰게 하면 되지 않을까 생각하였다.  

<br>

</br> 

name[20] 이므로 아무 문자를 20번 이상 입력하면,  

`ptr[20] = '\0';` 부분에 의해, 널 문자 (\0)가 게속 입력되고, 이에 age 메모리 부분에 0을 넘어가게 할 수 있을 것으로 보인다. 

<br>

</br> 

![image](https://github.com/user-attachments/assets/65377b22-66aa-4000-b821-ab63b35ff07e)

그래서, 파이썬 코드로 name 버퍼에 임의의 문자를 20번 임력하여서, 오버 버퍼플로우 문제를 발생시키는 익스플로잇 코드를 작성해보았다. 
이를 작성하고, 실행시켜 보았는데 flag 파일이 확인되지 않아서, flag 값을 얻을 수 없었다. 
<br>

</br> 

![image](https://github.com/user-attachments/assets/70bb6753-169a-4e15-97a7-0fa940f99304)

그래서, 임의의 문자열 20번을 입력하고, `b'\x00\x00\x00\x00'` 을 더 추가하여,

age 변수의 값을 0으로 설정하기 위한 4바이트짜리 널 바이트를 더 이어붙여 보았다. 
<br>

</br> 

![image](https://github.com/user-attachments/assets/cc0d4d92-ba13-46af-846f-1d0c5512d9dd)

수정한 코드를 다시 실행해보니, flag 파일을 찾을 수 있었고, 이를 출력해보니 flag 값을 얻을 수 있었다.

<br>

</br> 

```
DH{343bab3ef81db6f26ee5f1362942cd79}
```
<br>

</br> 

![image](https://github.com/user-attachments/assets/2674e446-30a4-41d5-ae30-a1acebeb8435)

문제를 해결할 수 있었다!
