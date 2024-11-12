<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Lord of SQL Injection "orc"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.11.05 풀이

![image](https://github.com/user-attachments/assets/f849af83-0d9f-4f6d-b8c1-77621e8fd15f)

해당 문제는 다른 사람의 라이트업을 보면서 공부하면서 문제를 풀어보려고 한다. 문제를 풀기 전에,

전체적으로 php 코드부터 살펴보려고 한다.  밑에 코드에 주석으로 정리하였다. 

<br>

</br>

```
<?php 
  include "./config.php";    //config.php 파일을 포함하여 DB 연결 정보 등 설정을 불러옴
  login_chk();               //사용자 로그인을 확인하는 함수
  $db = dbconnect();         //dbconnect() 함수를 통해 DB 연결 설정 / 연결 정보를 $db 변수에 저장
  
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");          //pw 값에 특정 문자열이 포함되어 있으면 "NO Hack ~_~"  메시지 출력
  $query = "select id from prob_orc where id='admin' and pw='{$_GET[pw]}'";      → SQL 인젝션 공격을 방지하려는 시도로 보임!
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello admin</h2>"; 
   
  $_GET[pw] = addslashes($_GET[pw]);                                         //pw 값에 슬래시 추가  → 특수문자 이스케이프 처리
  $query = "select pw from prob_orc where id='admin' and pw='{$_GET[pw]}'";  //id가 admin이고, pw가 입력된 값과 일치하는 행을 찾음
  $result = @mysqli_fetch_array(mysqli_query($db,$query));                   //쿼리 실행 + 결과 가져옴
  if(($result['pw']) && ($result['pw'] == $_GET['pw'])) solve("orc");        //pw 값이 DB에 존재하고, 입력한 pw 값과 동일한지 확인
                                                                               → 조건 만족 시, solve("orc") 함수 호출 
  highlight_file(__FILE__);       
?>
```
 다른 사람의 라이트업을 보니, preg_match 함수 부분에 집중하는 거 같았다. 

▣ preg_match($pattern, $subject, [ $matches]) 

    : 문자열 안에서 특정한 정규식 패턴의 존재 여부를 찾는 함수

     ○ $pattern : 문자열로 검색할 패턴, 특징

     ○ $subject : 입력 문자열

     ○ [ $matches ] : 사용 시 패턴에 해당하는 내용을 배열에 저장해놓음

 

  ▷ pw의 입력 값 필터링 규칙으로 prob, _, . , () 을 필터링한다는 것을 알 수 있다.


 

  그 다음은 addlashes 부분에 집중을 하는 거 같아 addlashes에 대해 살펴보려고 한다.

<br>

</br>

▣ addlashes() 

      : DB의 질의에서 처리할 필요가 있는 문자 앞에 \ 를 붙인 문자열 반환

        ▷ SQL 구문 내에서 문자가 데이터로 인식되어, 쿼리의 구조 변화 X

        → 주로 SQL 인젝션 공격을 방지하는 용

        ● 싱글 쿼트 ( ` ) → \`

         ● 더블 쿼트 ( " )→ \"

        ● 역슬래시 ( \ )→ \\

        ● NULL 문자 ( \0 )→ \\0

<br>

</br>

그래서 해당 문제를 해결하기 위해서는 

if(($result['pw']) && ($result['pw'] == $_GET['pw'])) 위 조건과 같이

admin 계정의 정확한 pw를 입력해야지 해결될 수 있을 것으로 보인다. 

<br>

</br>

일단 pw의 길이를 알아내기 위해, length() 를 이용해보려고 한다. 

length()는 SQL에서 문자열의 길이를 반환하는 함수이다.

문자열 내 문자의 개수를 세어 정수 값으로 반환한다.

비밀번호는 모르니 pw에는 아무거나 써주고, or 뒤에는 admin 이면서

pw의 길이를 하나씩 대입해보았다. 

`pw=1%27%20or%20id=%27admin%27%20and%20length(pw)=8%23` 를 입력하니,

Hello admin이 출력되었다. 이로써 pw의 길이는 8인 것을 알 수 있다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/821ec981-04da-4868-ac33-8c99afc56b99)

이제 실제 pw 값을 알아내기 위해, 각 자리에 0~9, A~Z, a~z 를 하나씩 대입해보며,

pw 값을 찾아내는 파이썬 코드를 작성해야 한다고 라이트업 글쓴이는 말한다. 

글쓴이의 코드를 참고하여, 파이썬 코드를 작성해보았다. 

<br>

</br>

![image](https://github.com/user-attachments/assets/bc4dbd73-ad99-4d92-99b7-edf5e8ae2499)

find_password() 를 통해, 비밀번호를 한 글자씩 찾아가고, 글자들을 돌아가면서 대입하였을 때, "Hello admin" 이라고 뜨면 비밀번호로 생각하게끔 하였다. 

그리고, 이를 8번 반복하여서 맞는 비밀번호를 찾아내는 코드이다. 

 <br>

</br>

해당 코드를 실행해보니, 앞 글자부터 차근차근 나오더니, 비밀번호 "  095A9852 " 인 것 알아냈다. 

해당 값을 쿼리에 입력해보니,

![image](https://github.com/user-attachments/assets/d8d8040d-79e0-4282-ae8e-5b769b515d96)

문제를 해결할 수 있었다. 
