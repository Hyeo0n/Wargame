<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Lord of SQL Injection "orge"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.11.05 풀이

![image](https://github.com/user-attachments/assets/8cdba879-ab18-44d8-be17-0ae17217b644)

위 문제는 아까 orc 문제와 유사해보인다. 다만 or과 and를 사용할 수 없기 때문에,

이를 || , &&를 사용해서 풀면 될 것 같았다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/9ddb5da3-17bc-40ed-9f1d-358843273a0f)

아까 pw의 길이를 알아보려고 작성한 쿼리에서 or과 and를 각각 ||과 &&로 바꿔어 작성하여,

입력하였더니, Hello admin이 나왔다. 이번에도 pw의 길이는 8개인 것을 추정된다. 

 <br>

</br>

아까 작성한 python 코드에서 해당 필터링을 우회할 수 있도록 수정하였다.

 <br>

</br>

![image](https://github.com/user-attachments/assets/7ca41b21-d594-447c-aecd-05d0c77e5876)

이번에도 아까처럼 하나씩 찾아지더니, 비밀번호 "  7b751aec " 인 것 알아냈다. 



실행시켜보니,

![image](https://github.com/user-attachments/assets/27ce45a3-cd47-4e8a-8f26-729aeaa984c7)

문제를 해결할 수 있었다. 
