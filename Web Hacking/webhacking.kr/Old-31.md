<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> webhacking.kr "Old-31"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.09.06 풀이

![image](https://github.com/user-attachments/assets/4897a4dc-5c04-43eb-abe0-543d91769d4a)

webhacking.kr 사이트에 접속했다.

<br>

</br>

![image](https://github.com/user-attachments/assets/0480d277-4b1e-4871-9003-90a27e39035e)

Old-31 문제를 들어가보니, 해당 화면이 출력되었다.

<br>

</br>

```
$port = rand(10000,10100);
$socket = fsockopen($_GET['server'],$port,$errno,$errstr,3) or die("error : {$errstr}");
```

해당 화면에 나온 위와 같은 PHP 코드를 해석해보니,

$port = rand(10000, 10100); 줄은 10000에서 10100 사이의 랜덤한 숫자를 포트 번호로 생성하는 코드인 것으로 보인다.

서버에 연결할 때, 사용할 포트를 실행할 때마다 10000~10100 사이의 포트 중 하나를 선택하게끔 하는 코드이다.

<br>

</br>
 
$socket = fsockopen($_GET['server'],$port,$errno,$errstr,3) or die("error : {$errstr}"); 줄은 

fsockopen() 함수를 통해 원격 서버와의 소켓 연결하는 시도하는 코드로 보인다.

여러 매개변수를 전달받는 것으로 보이는데, 차례대로 사용자가 URL 파라미터로 전달한 서버 주소, 앞에서 생성한 포트 번호, 연결이 실패했을 때 발생하는 에러 번호, 연결이 실패했을 때 발생하는 에러 메시지 인 것으로 보인다. 또한, 연결 시도에 사용할 타임아웃 시간은 3초로, 3초동안 연결을 시도하고 실패하면 에러를 반환한다. 즉, 지정된 서버의 특정 포트로 TCP 연결을 시도하는 것으로 보인다. 

뒤에 or die("error : {$errstr}"); 를 살펴보니, 소켓 연결에 실패하면, die() 함수를 사용하여 프로그램이 즉시 종료되며 에러 메시지를 출력하는 것을 보인다. 

<br>

</br>

근데 밑에 경고문을 살펴보니,

![image](https://github.com/user-attachments/assets/2d1f4f2d-0a71-436e-88ae-029d81f492e9)

현재 문제에서 접근하려는 서버가 180.229.202.16 인 것 같은데, 이에 연결을 할 수 없다는 경고문이다.

해당 서버에 연결되려면, 10000번부터 10100번의 포트를 열어주어야 할 것으로 예상된다.

 <br>

</br>

그래서, 내 현재 컴퓨터 서버에서 10000 ~ 10100 사이의 포트를 열어놓기 위해, 간단한 파이썬 코드를 작성해보았다.

![image](https://github.com/user-attachments/assets/655a0143-c1c4-4fc6-83fb-d88115999352)

해당 코드는 nc를 이용해서 10000 ~ 10100번 포트를 열게 하는 코드이다.
 <br>

</br>

그리고, 외부에서 해당 포트에 접근하게끔 설정하기 위해, 포트 포워딩을 실시하였다.

네트워크를 LG U+를 사용해서, 192.168.219.1에 접속했다.

![image](https://github.com/user-attachments/assets/1c23b776-f422-4adc-b6eb-6c75f9cb8399)
![image](https://github.com/user-attachments/assets/804119e1-087b-41a6-906a-bf66c88403f5)
![image](https://github.com/user-attachments/assets/c7de9e53-391d-408e-be98-1354d8b64b58)

이렇게 새롭게 포트 포워딩 규칙을 제정해주었다.

 <br>

</br>

![image](https://github.com/user-attachments/assets/0e85732c-1c95-4e8c-b3c3-053a0e642c01)

그 후, 아까 만들었던 파이썬 파일을 명령 프롬프트 창에서 실행시키고,

다시 Old-31 문제 페이지를 새로고침하면
 <br>

</br>

![image](https://github.com/user-attachments/assets/f7bc6acd-23ba-4025-9e3a-b62f9eaaaabb)

경고문이 사라진 것을 확인할 수 있다.


다시 명령 프롬프트 창을 확인해보면, 

 <br>

</br>

![image](https://github.com/user-attachments/assets/b44b6005-e27c-4528-9bf2-9e8ce2484ed0)

flag 값이 출력된 것을 확인할 수 있다

```
FLAG{i_have_a_big_and_beautiful_server}
```
 <br>

</br>

![image](https://github.com/user-attachments/assets/a04eac9a-cf7a-4a66-90ac-71240a0a4be2)

