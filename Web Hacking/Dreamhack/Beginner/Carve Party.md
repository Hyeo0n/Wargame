<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "Carve Party"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.08.14 풀이

![image](https://github.com/user-attachments/assets/9dc72f97-dd6f-4a4b-848b-a9a2920aafa6)

Dreamhack 워게임에 있는 비기너 단계의 Carve Party를 풀어보려고 한다. 

<br>

</br>

문제 파일을 다운받으니, 

![image](https://github.com/user-attachments/assets/a888313a-8c9a-4707-98a2-af6c07918d75)

HTML 문서 하나만 있었다.

<br>

</br>

![image](https://github.com/user-attachments/assets/899ecc2d-c470-4df0-acb4-47e71b712453)

들어가보니, 위와 같은 화면이 출력되었고, 호박 모양을 클릭할 때마다, more clicks to go! 앞에 숫자가 계속 줄어들었다.
개발자 도구를 통해, HTML 태그를 살펴보려고 한다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/012e6ffc-e318-40c9-9c55-f21f197a5db3)

살펴보니, $('#jack-target').click() 함수가 counter의 값을 점점 늘려주는 내용이 있다. counter는 사용자가 호박을 클릭한 횟수를 나타내는 것 같다. 그래서, flag 값이 호박을 10000번 누르면 나오는 것 같아서, counter 값을 10000로 수정하면 될 것 같다고 생각했다.

<br>
</br>

![image](https://github.com/user-attachments/assets/eeb0c108-0977-494b-bea1-a031a107a6ce)
![image](https://github.com/user-attachments/assets/61f6e90c-bbbb-4ac6-91a1-d84e60ff92d4)

counter 값을 10000으로 설정했는데, flag는 뜨지 않았다. 

<br>

</br>

그래서 기존의 html 파일을 손 봐서, counter가 10000이 되도록 수정하였다
<br>
</br>

![image](https://github.com/user-attachments/assets/af360b3b-59dc-41f7-9c85-28ef260677b6)

counter 값이 한 번 클릭될 때마다, 1000씩 증가하게끔 수정하였다.

<br>

</br>


![image](https://github.com/user-attachments/assets/763fa450-976d-46f5-9cdd-21835405e4f4)
그렇게 10번을 클릭하니, flag 값이 출력되었다.

<br>
</br>

```
DH{I_lik3_pumpk1n_pi3}
```

<br>
</br>

![image](https://github.com/user-attachments/assets/cc54a7dc-0468-4bf6-9bc6-c9b2f967c62c)

이렇게 문제를 해결할 수 있었다.
