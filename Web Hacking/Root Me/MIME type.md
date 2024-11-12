<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Root Me File upload - MIME type Write-UP</h1>
</body>
<br>
<br>
</html>

2024.11.07 풀이

![image](https://github.com/user-attachments/assets/3ad0240f-9c04-4d22-9ed6-c9e1d69afd7d)

문제 설명을 보니, PHP 코드를 업로드하여, 갤러리를 해킹하라고 한다.

.passwd 파일에 플래그가 있는 것으로 보인다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/a0f468e0-8558-43dd-8fb4-deba8f042cb0)

일단 서버에 들어가보니, 해당 화면이 나왔다.

<br>

</br>

![image](https://github.com/user-attachments/assets/f7b0ed6e-22dc-45cd-969e-f2dad28777d0)

defaced에는 이런 화면이 출력되었다.

 <br>

</br>

![image](https://github.com/user-attachments/assets/2f1cb3b0-6da4-4f78-b179-156963ba7364)

upload를 누르니, 이렇게 파일을 업로드하는 부분이 나왔다.

여기에 작성한 PHP 파일을 업로드하면 될 것으로 보인다.

밑에 설명을 보니, GIF, JPEG, PNG 확장자만 업로드할 수 있을 것으로 보인다. 

 <br>

</br>

![image](https://github.com/user-attachments/assets/88a08b69-cd67-4d02-97e2-43c5d4e503cd)

 pirate 창은 들어가보니, 해당 화면이 나왔다.

<br>

</br>

일단 서버만 살펴봤을 때, 감이 안 와서 MIME type이 뭔지부터 알아보기로 하였다.

MIME type은 인터넷에서 전송되는 다양한 종류의 데이터를 식별하기 위한 표준 형식을 말한다. 

주로 웹 브라우저가 웹 서버로부터 받은 데이터를 해석할 때 사용된다고 한다.

아마 아까 upload 창에서 GIF, JPEG, PNG 로 파일 확장자가 제한되어 있는 걸 보니, 이 MIME type에

해당 확장자로 지정되어 있고, 나머지는 이 MIME 에서 필터링될 것으로 보인다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/5797dd59-312c-447a-adf6-c7b158f5473b)

버프 스위트를 사용하여, HTTP 전송 과정을 살펴보았다.

일단, 아까 작성한 webshell.php.jpeg 파일을 다시 업로드 하니 Request 창에 저렇게 기록되어 있었다.

<br>

</br>

![image](https://github.com/user-attachments/assets/89637517-b2f8-4900-831f-45ebda3ccc9a)

저기서 Contet.type은 그대로 jpeg로 두고, 파일명을 webshell.php로 바꾸고, Forward 버튼을 눌렀다.

<br>

</br>

![image](https://github.com/user-attachments/assets/3e77490d-085f-446a-9d70-b69e811b779f)

파일이 php 확장자로 잘 업로드된 것을 확인할 수 있다.

<br>

</br>

![image](https://github.com/user-attachments/assets/4e8d0272-38f7-4ec5-83bc-88431b7fafb6)

다시 upload 페이지에 들어가서, 업로드된 weshell을 클릭하여 들어간다.  

<br>

</br>

![image](https://github.com/user-attachments/assets/6ffd8e4e-e0e5-4afd-892f-f68e9c3003dd)

들어가보면, URL이 이렇게 뜨는데, 이 뒤에 ?cmd=cat%20/challenge/web-serveur/ch21/.passwd

를 붙여 입력한다.

그러면, 플래그 값이 들어있는 .passwd 파일의 내용을 출력시킨다.

<br>

</br>

```
a7n4nizpgQgnPERy89uanf6T4
```
<br>

</br>

![image](https://github.com/user-attachments/assets/5713403b-e9d5-42be-b368-83fda40af77b)


