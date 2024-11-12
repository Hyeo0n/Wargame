<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> webhacking.kr "Old-43"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.11.07 풀이

![image](https://github.com/user-attachments/assets/083fbb31-679a-4b8e-857f-b2536dcbf0ad)

해당 문제로 들어가보니, 웹 셸을 업로드하여, flag를 출력하라고 한다.

밑에 파일 업로드할 수 있는 곳에 웹 셸을 작성하여 업로드하면 될 것으로 보인다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/d13ea5ef-4990-4296-8c1c-bb76380918bf)

그냥 제출을 눌러보니, type not detached 라는 문구가 뜬다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/bbbd852b-91ae-4dfd-83fa-23458ea9bfea)

그래서 위와 같은 간단한 PHP 코드를 작성해보았다. 

$_GET["cmd"] 는 URL 파라미터로 전달된 cmd 값을 받아서 $cmd 변수에 저장하고,

system($cmd) 함수는 $cmd 변수에 저장된 명령어를 서버의 OS에서 실행하고, 브라우저에 출력하게끔 한다.

<br>

</br>

![image](https://github.com/user-attachments/assets/62cd38f4-b5fc-45e6-be34-6206adbdb3f9)

위 코드를 업로드해보니, wrong type이라고 뜬다.

<br>

</br>

![image](https://github.com/user-attachments/assets/1cc54b69-ea13-4c65-8eb9-e875365c5588)

위 php 파일을 jpeg로 바꿔서 내보니, Done! 이라고 뜨며, 제출은 되는 것을 확인할 수 있다. 

하지만, 아무래도 이미지 파일 형태로 업로드했다보니, 웹 셸로서 실행은 되지 않아,

아무런 변화도 얻을 수 없는 것으로 보인다. 

  <br>

</br>

![image](https://github.com/user-attachments/assets/06c0e3d6-9ac6-453c-8b5a-ae522ac2cd3f)

이번 문제도 마찬가지로 버프 스위트를 사용하여, HTTP 전송 과정을 살펴보았다.

일단, 아까 작성한 webshell.php.jpeg 파일을 다시 업로드 하니 Request 창에 저렇게 기록되어 있었다.

  <br>

</br>

![image](https://github.com/user-attachments/assets/db1f0187-67c2-4eef-86ab-ac81f15bf257)

저기서 Contet.type은 그대로 jpeg로 두고, 파일명을 webshell.php로 바꾸고, Forward 버튼을 눌렀다.

<br>

</br>

![image](https://github.com/user-attachments/assets/87fb8d90-757f-4b6b-9724-49885ae65a57)

누르니, webshell.php 파일 업로드가 성공되었다!

<br>

</br>

![image](https://github.com/user-attachments/assets/d4e230c9-eab3-4a96-9983-0f2f65df6fa7)

./upload/webshell.php 링크를 클릭하여 들어가보니, 위와 같이 flag 값을 얻을 수 있었다.

<br>

</br>

```
FLAG{V2hhdCBkaWQgeW91IGV4cGVjdD8=}
```
<br>

</br>

![image](https://github.com/user-attachments/assets/85556c86-857a-4a18-9525-26d507617ac4)



