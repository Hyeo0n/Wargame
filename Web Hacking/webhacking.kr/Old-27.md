<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> webhacking.kr "Old-27"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.10.01 풀이

![image](https://github.com/user-attachments/assets/9fdb2823-8217-4907-9195-dfc161c00a2f)

문제에 들어가보니, 해당 입력칸이 있는 페이지가 출력되었다. view-source 부분을 더 살펴보도록 하겠다.

<br>

</br>

![image](https://github.com/user-attachments/assets/cc6a0cef-b052-47f7-8d0d-3c7c5799e87f)

preg_match 함수는 사용자가 입력한 값에서 SQL 쿼리와 관련된 여러 문자열이 포함되어 있는지 확인하여, 
해당 문자열이 포함되면, no hack 메시지를 출력하고 종료하는 것으로 보인다. 

해당 필터링 조건을 만족하면, 사용자가 입력한 $_GET['no'] 값을 직접 쿼리에 넣는 것으로 보인다.

" SELECT id FROM chall27 WHERE id='guest' AND no={$_GET['no']} " 해당 쿼리를 통해 DB에서 guest의 id와 no를 조회하는 것으로 보인다. 

현재 guest와 admin이라는 두 개의 id가 있는, 만약 쿼리 결과가 guest면 "guest"를 출력하고, admin이면 아마 플래그 값이 출력되는 거 같아 보인다.

주석을 확인해보니, admin의 no는 2로 보인다. 

 <br>

</br>

일단, 입력값을 전달받은 쿼리에서 이미 id는 guest라고 지정이 되어 있기 때문에, 이 부분이 false가 되게 해야 하고, no 값을 2로 하고, 뒤에 die 부분은 주석처리를 해야한다. 이 것을 모두 만족시킨 값을 입력해야 할 것으로 보인다. 

0 )or no=2# 를 사용하면, ) 를 사용하면, 위 해당 조건을 만족할 것 같아 보인다. no에 들어가는 0 값이 앞에 guest와 묶이기 위해서 사용하였고, 뒤에 die 부분을 주석처리하기 위해 #을 사용하였다. 하지만, #과 =과 공백이 필터링 조건에 걸려있기 때문에, " # "을 " -- " 로, " = "을 "like" 로, 공백을 URL 인코딩인 "%09" 을 사용해보도록 하겠다. 

0)%09or%09no%09like%092%09--%09 를 입력해보도록 하겠다. -- 주석 처리 뒤에도 공백 %09를 사용한 이유는 -- 뒤에 공백 처리가 없으면 주석처리가 되지 않는다고 하여서 넣어주었다. 
 <br>

</br>

![image](https://github.com/user-attachments/assets/7117bad5-2ad7-4331-8b14-7890ea7902c9)

![image](https://github.com/user-attachments/assets/883361fa-132f-49e6-b1f8-21de0c600476)

위에서 작성한 값을 파라미터에 입력하니, 문제를 해결할 수 있었다.
