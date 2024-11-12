<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "simple_sqli_chatgpt"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.10.01 풀이

<br>

</br>

![image](https://github.com/user-attachments/assets/5ff94297-b252-4ad6-b235-aadb0e3c4645)

문제 설명을 보니, 플래그는 flag.txt에 FLAG 변수에 들어있는 것 같고, chat gpt를 사용하여 풀어보라고 한다.
chat gpt는 하다가 막히면 사용해봐야겠다.
<br>

</br>

![image](https://github.com/user-attachments/assets/b99ed812-55cb-4f96-99b1-87a5ba505549)

![image](https://github.com/user-attachments/assets/9463f7e7-51db-4924-80e5-93b0dac937c5)

우선 서버에 들어가보았다. 
들어가보니, 우선 로그인 페이지가 보인다. 아마 로그인을 해야 다른 페이지도 확인할 수 있을 것으로 보인다. 
서버에서는 더 확인할 것이 없어보여서 다운받은 코드를 살펴보려고 한다.

![image](https://github.com/user-attachments/assets/fa3a0bea-d088-476d-acaf-20c598d9b76e)

![image](https://github.com/user-attachments/assets/011b906f-9162-4c94-836d-4100e0962797)
<br>

</br>
<br>

</br>


## login 페이지
![image](https://github.com/user-attachments/assets/4b66f161-47c1-4c65-bd4c-6f9a9850e8f1)

로그인 페이지의 코드를 살펴보니, 로그인 페이지의 userlevel이라고 입력하는 칸에 우리가 입력한 값이
쿼리문에 삽입되서, query_db로 가는 것이 보인다. 
또한, id가 admin인지, userlevel이 0인지 확인하고, 플래그값을 출력해주는 것 같아 보인다.

<br>

</br>

## query_db 페이지
![image](https://github.com/user-attachments/assets/26b03f1b-ec28-4377-a804-052c1046e679)

query_db 부분을 살펴보니, 우리가 입력한 쿼리문을 배열로 입력해서 리턴해주는 것 같이 보인다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/e295a224-f546-4af0-9c33-693f632dcc10)

그래서, 로그인 페이지에 userlevel 칸에 0'and userid='admin 라고 입력해보도록 하겠다. 해당 userlevel 칸은 userlevel을 입력받는 칸이므로, 
0을 입력하고, 뒤에 ' 로 문자열을 끊어주고, userid가 admin 인 것도 언급하여, 레벨이 0이고, userid가 admin 요소를 찾아오도록 하였다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/7aaeb633-cc60-4406-af74-cfd9786ece9d)

플래그 값이 성공적으로 출력되었다. 
<br>

</br>

```
DH{chatGPT_told_me_a_lullaby}
```
![image](https://github.com/user-attachments/assets/c81ac48c-aad2-445a-8668-d658761ef962)
