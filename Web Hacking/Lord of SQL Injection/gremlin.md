<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Lord of SQL Injection "gremlin"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.10.01 풀이

![image](https://github.com/user-attachments/assets/783feb7a-f158-426b-994d-44bf28386e8d)

![image](https://github.com/user-attachments/assets/031ec1a2-1823-4f86-96ae-49c16c48e853)
 <br>

</br>

" select id from prob_gremlin where id='' and pw=''" 이 부분을 보니, 쿼리 구조에서 id와 pw 값이 사용자의 입력으로 대체되는 거 같아 보인다. 

그 뒤 PHP 언어로 된 코드를 천천히 살펴보았다.

로그인 체크 함수가 실행되고, DB에 연결하는 함수가 실행된다. 그 후, id와 pw 파라미터에 특정 문자열을 필터링하고 있는 로직 또한 확인할 수 있다.

그 후, query = "select id from prob_gremlin where id='{$_GET['id']}' and pw='{$_GET['pw']}'";  를 통해,

id와 pw 값을 사용해 SQL 쿼리를 생성한다. 쿼리를 실행하고 결과를 가져오는데, 결과에 id 가 있는 경우는 solve("gremlin") 함수가 실행되는 것으로 보인다.

 <br>

</br>

파라미터에 id를 입력해주면 문제가 해결될 것으로 보인다.

현재 파라미터 뒤에 ?id=admin%27%23 을 입력해보려고 한다.

URL 인코딩에서 %27은 '(작은 따옴표)이거, %23은 # 을 가리킨다. 

애초에 사용자의 입력값 "$_GET['id']" 가 들어갈 때, 작은 따옴표로 감싸져 있는데, 한 번 더 ' 을 사용함으로써, admin 문자열을 닫고, 뒤에 # 과 구분한다. SQL에서 " # " 은 주석을 의미하므로, 
그 뒤에 나오는 모든 내용은 무시된다. 즉, pw 부분은 무시되고, 쿼리는 id='admin' 부분만 검사하게 되어 "admin" 계정으로 로그인할 수 있게 된다. 
 <br>

</br>

![image](https://github.com/user-attachments/assets/5d50805e-8ebc-4f5b-b140-b2cf50504950)

해결하였다!
