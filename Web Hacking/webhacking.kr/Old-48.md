<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> webhacking.kr "Old-48"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.11.07 풀이

![image](https://github.com/user-attachments/assets/3eab4012-9479-4757-95b0-3dd07b28e21c)

![image](https://github.com/user-attachments/assets/f9b63bf1-5a37-4088-9e89-51090495d8ae)

![image](https://github.com/user-attachments/assets/72725223-4314-42a8-97d7-b8fb93f79a6c)

<br>

</br>

![image](https://github.com/user-attachments/assets/c45ed43b-8571-482c-a1b2-8685a223bcfb)

해당 문제로 들어가보니, 저런 것들이 쭉 나열되어 있길래 뭔가 했는데,

입력칸에 안녕을 치고 send 해보니, 저렇게 뜨는 것을 발견했다. 

메모와 파일을 업로드한 것이 쭉 나열되어 있는 것을 알았다. 

 <br>

</br>

위 문제들처럼 php 파일을 확장자를 변경하여 업로드해보았는데, 문제가 해결되지 않았다. 

그래서 문제를 어떻게 접근해야 할지 너무 감이 안 와서 다른 사람의 라이트업을 참고해보려고 한다.

다른 이들의 라이트업을 보니, 해당 문제는 command injection 이라고 한다. 

Command Injection 이 뭔지 찾아보니, 

이용자의 입력을 시스템 명령어로 실행하게 하는 취약점이라고 한다.      

명령어를 실행하는 함수에 이용자가 임의의 인자를 전달할 수 있을 때 발생한다고 한다. 

 <br>

</br>

다른 이들의 라이트업을 보니, 삭제할 때를 잘 활용해야 된다고 한다. 

파일이 삭제될 때, rm 명령어가 사용이 되는데, rm 명령어를 수행할 때는

파일의 이름을 사용하며, 이 값은 사용자가 직접 입력하게 된다. 

이 과정에서 사용자의 입력 값에 대한 검증 로직이 존재하지 않으므로 커맨드 인젝션이 가능하다고 한다.

그래서, 파일 이름을 아무렇게나 작성해주고, ;ls 를 통해 커맨드 인젝션을 수행하면 된다고 한다.

<br>

</br>

![image](https://github.com/user-attachments/assets/c1fa3b4e-6e6e-4577-9969-9f81a8f68d84)

![image](https://github.com/user-attachments/assets/f3e79b9d-bb53-447f-b1e6-3f3bfff75dd5)

메모를 123 이라고 아무렇게나 입력하고, 파일명도 1;ls 로 임의로 작성하여 게시하였다. 

저기서 Delete를 누르면,

![image](https://github.com/user-attachments/assets/b07e326d-0607-46cf-9823-797234ce394f)

플래그 값이 출력된다!!

<br>

</br>

```
FLAG{i_think_this_chall_is_cool}
```
<br>

</br>

![image](https://github.com/user-attachments/assets/4afdd042-7eaa-41d1-bac3-dc6dfb6e3aa8)

문제를 해결할 수 있었다...

하지만 남의 라이트업을 참고해서 푼 거라서, 나중에 

이와 유사한 command injection 문제를 풀어봐야겠다.

 
