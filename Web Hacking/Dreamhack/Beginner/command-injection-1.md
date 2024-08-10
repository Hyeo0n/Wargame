<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "command-injection-1"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.08.07 풀이

![image](https://github.com/user-attachments/assets/8f249635-1e8a-47c7-9b9e-712445524af1)

https://chs777888.tistory.com/53

에서 Command Injection에 대해서, 학습한 후,

이를 실습으로 익히기 위해, Dreamhack 워게임에 있는 비기너 단계의

command-injection-1 풀어보려고 한다. 

<br>

</br>
  
![image](https://github.com/user-attachments/assets/32ecbe24-03c5-476e-88d4-8932ad5e87f5)

우선 서버에 접속해보니, Home 페이지에 Ping playgrond 라는 내용이 출력되었다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/177844aa-89f8-4b1a-8260-5a313d9019e1)

![image](https://github.com/user-attachments/assets/925ad9c0-2790-46c0-b878-025a3aacb265)

ping 페이지를 보니까, 저기 입력칸에 IP 주소를 입력하면 ping을 쏘는 것으로 추측된다. 

흐리게 8.8.8.8로 쓰여 있길래, 그대로 입력해보니까, 해당과 같이 ping을 보낸 결과가 나왔다. 3번 ping을 보내는 것을 확인할 수 있다.

이제 다운받은 파일을 살펴보려고 한다.

 <br>

</br>

## flag.py
 ![image](https://github.com/user-attachments/assets/92727b1f-fe19-406f-9aab-db027b1ed65c)

flag.py를 확인해보니, flag 값이 REDACTED라고 암호화되어 있었다.

 <br>

</br> 

## app.py
 
![image](https://github.com/user-attachments/assets/2dfc7b73-0970-41a6-8684-938a4198e2e5)

그 후 app.py 파일을 살펴보았다. 

 <br>
</br>  

![image](https://github.com/user-attachments/assets/279c2273-40d0-47ea-a05d-46feb6b33ae8)

그 중 ping 페이지 부분 코드를 살펴보면, request.form.get을 통해, host의 값을 불러오고, 이를 바로 뒤 cmd 변수에다가 포함시키는 것을 확인할 수 있다. 여기 부분에서 입력값 검증이 제대로 이루어지지 않기 때문에 command injection 취약점이 발생하는 걸로 보인다. 그 후, output 변수는 /bin/sh을 실행하는데, -c 옵션으로 인자를 받고, 인자로 cmd 변수를 실행시킨다. 

8.8.8.8 IP 주소에 메타 문자와 쉘 명령어를 통해 파일들을 확인하는 인젝션을 우선적으로 시도해보려고 한다.

8.8.8.8";ls"  이라고 입력했지만, 지정된 형식과 일치되지 않는지 실행되지 않았다.

 <br>
 
</br>   

![image](https://github.com/user-attachments/assets/669ab8c2-9086-44b2-b054-69c8cb450039)

![image](https://github.com/user-attachments/assets/e0b91008-8ff2-4e41-8934-cf3c8944b839)

왜 실행이 되지 않는지 ping 페이지를 개발자 도구를 사용해서 코드를 살펴보았다. Host 입력칸 부분 코드를 살펴보니, pattern="[A-Za-z0-9.]{5,20}" 이라는 것을 볼 수 있는데, 이를 해석하기 위해 찾아보니, 알파벳 대소문자와 점 (.)을 이용한 5~20글자인 것을 알아낼 수 있었다.

그래서 그냥 저 부분을 지우고, 아까 명령어를 다시 입력해보려고 한다.

 <br>
 
</br>  

![image](https://github.com/user-attachments/assets/b94d8248-fec7-4ae6-8d89-7674eb2a59d2)

![image](https://github.com/user-attachments/assets/8ac6b422-a315-47d3-bcba-7cc2cad9567e)

실행해보니, flag.py가 있는 것을 확인할 수 있었다. 

flag값을 가지고 있는 flag.py를 실행시켜 flag값을 출력하게끔 명령어를 작성해보려고 한다. 

```
8.8.8.8";cat "flag.py
```

이라고 작성하고, 이를 입력칸에 다시 입력해본다.

 <br>
 
</br>   

![image](https://github.com/user-attachments/assets/c32e9892-f069-42af-9198-dc25ef26cc0a)

![image](https://github.com/user-attachments/assets/21307670-a8ec-4926-89f5-f77d602951da)

FLAG 값이 화면에 출력된 것을 확인할 수 있다.

 <br>
</br>  
 
```
DH{pingpingppppppppping!!}
```

  <br>
</br>  

![image](https://github.com/user-attachments/assets/aa31006e-b8c2-4d88-b0d4-c8a1c3a83870)

이렇게 해당 문제를 해결할 수 있었다.
