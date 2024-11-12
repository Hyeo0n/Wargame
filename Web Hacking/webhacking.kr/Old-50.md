<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> webhacking.kr "Old-50"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.10.01 풀이

![image](https://github.com/user-attachments/assets/457f0455-aafd-42f1-8b00-39bcc0c1faf8)

문제에 들어가보니, 해당 입력칸이 있는 페이지가 출력되었다. view-source 부분을 더 살펴보도록 하겠다.

<br>

</br>

![image](https://github.com/user-attachments/assets/11d89bc9-07d1-44e1-a80b-5d5bcfba9839)

사용자의 id와 비밀번호를 입력받아, 데이터베이스에서 확인하는 로직으로 보인다. addslashes 함수는 사용자의 입력 값에서 특수 문자를 이스케이프 처리하는 것으로 보인다. 특수 문자 앞에 " \ "를 추가하여 필터링하는 것으로 보인다. mb_convert_encoding은 입력값을 utf-8에서 euc-kr 로 변환하는 것으로 보인다. 또한 preg_match를 통해 입력값에 union, |, >, < 등의 문자가 포함되어 있으면 프로그램을 종료하는 것으로 보인다. 

입력한 값을 DB에서 조회하고, lv 값을 확인하고, 각각 1, 2일때마다 level 1, level 2로 출력되는 것으로 보인다. 그리고, lv값이 3인 경우 문제가 해결될 것으로 보인다. 

 <br>

</br>

아까 풀었던 문제와 유사하게, 앞에 id 값은 무효하고, lv이 3이고, 뒤에 pw 부분은 주석처리를 하는 것을 작성하면 될 것 같다. 0' or lv=3# 을 필터링 조건을 우회해서, 0%fe%27%09or%09lv%09like%093%23 로 작성하고 입력해보았다.

하지만 예상과 다르게 wrong이 뜨고 문제가 풀리지 않는다. 똑같은 형식에 lv 1과 2를 확인해보니, 이들은 정상적으로 출력되었다. 이를 통해, DB에 lv=3인 정보가 존재하지 않는 건가? 라는 생각이 들었다. DB에서 정보를 빼내는 방식이 아니라 select을 사용해서 lv가 3인 정보를 출력시켜야 하는데, id에서 union 사용이 금지되어 있어서 union 인젝션도 불가능하기 때문에 딱히 방법이 없어보인다..

결국 손을 댈 수 있는 건 pw 파라미터이기 때문에 이를 손 대보려고 한다.

pw는 md5()로 묶여있는데, 이는 ( , ), " 등을 사용할 수 없지만, 주석은 필터링되지 않기 때문에 주석처리를 이용해 접근해보려고 한다. 

select lv from chall50 where id='0%fe%27/*' and pw=md5('*/union%09select%093%23')

이를 통해, 중간에 and pw=md5 을 주석처리해서 무효시키고, union 인젝션을 시도함으로써 lv이 3인 결과를 얻어보려고 한다.

<br>

</br>

![image](https://github.com/user-attachments/assets/76a3bebb-2ffb-4ed4-8363-1e1775dcf995)

![image](https://github.com/user-attachments/assets/ed71cfad-d718-4e32-8136-5f1f249f9137)

이렇게 문제를 해결할 수 있었다.
