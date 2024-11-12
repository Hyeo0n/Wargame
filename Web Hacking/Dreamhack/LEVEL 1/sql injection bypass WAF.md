<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1>  Dreamhack "sql injection bypass WAF"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.11.05 풀이

![image](https://github.com/user-attachments/assets/04d0885b-2149-4f32-bcc2-48c91ffe6403)

<br>

</br>

![image](https://github.com/user-attachments/assets/f578b9dd-b406-4188-827a-d5e61a82e35e)

우선 서버에 접속해보니, 해당과 같은 화면이 보였다. 아마 uid를 입력받고, 이를 쿼리에 집어넣어

결과를 출력해주는 것으로 추측된다..

<br>

</br>

![image](https://github.com/user-attachments/assets/93952d52-b86b-476a-a1ff-47a3f34471ea)

임의로 1을 입력해보니, 화면에 1이 출력된 것을 발견할 수 있었다. 

이제 코드를 살펴보려 한다.

<br>

</br>

![image](https://github.com/user-attachments/assets/37820ae2-d547-4437-9d3c-fea2c7ce0eb2)
![image](https://github.com/user-attachments/assets/4de41a3d-6ec8-4b6f-8dbd-7b3026637c2b)

코드를 살펴보니, GET으로 입력값을 받아서 check_WAF 함수에 대입해서 union, select 등의 키워드를

필터링하는 것을 볼 수 있다.  아마 이 함수는 SQL 인젝션 방지용으로 삽입된 것 같다. 필터링된 uid 값으로

DB에 쿼리를 실행하고, 결과를 출력하는 것으로 보인다.

<br>

</br>

![image](https://github.com/user-attachments/assets/c643e807-96b4-45ea-8cd2-c4f5e2ff0aa7)

sql 코드로 살펴보니, users라는 데이터베이스를 생성하고, 해당 데이터베이스에 대해 모든 권한을

부여하는 것 같다. 그리고 해당 DB에 테이블을 생성하여, 데이터를 삽입하는 것으로 보인다. 

해당 테이블에 여러 uid들이 있고, 그 중 admin의 upw가 FLAG인 것처럼 보인다. 

<br>

</br>
 
코드들을 살펴보니, 필터링 부분이 대소문자 구분을 하지 않을 것을 확인할 수 있다. 
  
따라서 대문자로 입력하면 필터링되지 않을 것으로 보인다! 띄어쓰기는 필터링이 되어 있기 때문에 %09로 url 인코딩 값을 입력하여, 
  
페이로드를 작성해보려고 한다. 또한, UNION 앞에의 구문에서 반환되는 column 수와 뒤의 구문에서 반환되는 column의 수가 같아야 하는 점을 유의하여 작성해보려고 한다. 
  
user라는 테이블에는 idx, uid, upw 이렇게 칼럼이 3개 있기 때문에 이를 반영하여 작성해보면,

`%27Union%09Select%09null,(Select%09upw%09From%09user%09where%09uid='ADMIN'),null%23`

이렇다.

<br>

</br>

실제로 대입해보니,
![image](https://github.com/user-attachments/assets/8a3fd1ad-ef55-404a-b194-daeb1af6e709)
FLAG 값이 출력되었다!!

<br>

</br>

```
DH{bc818d522986e71f9b10afd732aef9789a6db76d}
```
