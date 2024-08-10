<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "simple_sqli"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.08.02 풀이

![image](https://github.com/user-attachments/assets/b166718c-3703-40d4-9ebf-721c79747ed2)

https://chs777888.tistory.com/50

에서 SQL Injection에 대해서, 학습한 후,

이를 실습으로 익히기 위해, Dreamhack 워게임에 있는 1단계의

simple_sqli을 풀어보려고 한다. 

<br>

</br> 

![image](https://github.com/user-attachments/assets/8ec7602d-2b97-41f3-be3f-efe1f4e03093)

![image](https://github.com/user-attachments/assets/826fa783-121e-4d7c-9fee-3fcdf848e4ee)

우선 서버에 먼저 들어가보았다. 들어가보니, 그냥 일반적인 로그인 창만 보이고 별다른 단서는 없어서 코드를 살펴보도록 해야 겠다.

<br>

</br>  

![image](https://github.com/user-attachments/assets/720e3ebb-050c-4ccd-bd08-f8bd0c08a5e9)

![image](https://github.com/user-attachments/assets/1b5bd254-4f2d-4f43-8987-b8cb5e785df5)

 <br>

</br>  
<br>

</br>  

![image](https://github.com/user-attachments/assets/99f23888-f74e-4de9-a87e-b4c832ac232c)

코드 중에 DB에 관한 부분을 살펴보니, 아이디-비밀번호 쌍으로 userid-userpassword , guest-guest 가 저장되어 있고, admin 계정은 랜덤으로 16바이트 문자열로 비밀번호가 배정되는 것을 확인할 수 있다. 

 <br>
 
</br>  

![image](https://github.com/user-attachments/assets/b6dbee49-a9e6-4210-a736-2a8a56ecc3be)

밑에 login 페이지 관련 코드를 보니, 이용자에게 입력값인 아이디와 비번을 입력받고, 동적으로 쿼리문을 생성하는 것을 볼 수 있다.  그리고 이를 query_db 함수에서 전달하는 것을 볼 수 있다. 이를 통해, 입력 값에다가 쿼리문을 작성해서, SQL Injection 공격을 실행할 수 있을 것을 보인다. 뒤를 살펴보니, admin 계정으로 로그인하면, 화면에 flag 값이 출력되는 내용이 보였다. admin 계정으로 무조건 로그인되도록 로그인을 우회하면  flag 값을 얻을 수 있을 것 같다고 생각이 들었다. 

  <br>

 
</br>  

 admin 비밀번호를 몰라도, admin 계정으로 로그인되도록 하는 쿼리문을 작성해보려고 한다. DB의 데이터를 조회하는       SELECT 문을 사용해서 작성해보도록 하겠다.
 
  <br>
</br>  

```
SELECT * FROM users WHERE userid="admin"-- " AND userpassword="
```

   <br>
</br>  

 이렇게 쿼리문을 작성하여, userid 조건만 처리하도록 해서, 비밀번호 상관없이, admin 계정으로 로그인 시도하면, 무조     건 로그인되도록 하는 쿼리문을 작성해보았다. 

그 후 로그인 페이지에 admin"-- 과 아무 비밀번호만 쳐보니, 

   <br>
   
</br> 
 
![image](https://github.com/user-attachments/assets/9124e76a-59fd-4733-abdb-d0df98589451)

![image](https://github.com/user-attachments/assets/79c0f8ff-69e0-42fa-a57c-a0225ec37ab3)

화면에 flag값이 출력된 것을 확인할 수 있다!

 <br>
  
</br>  

```
DH{c1126c8d35d8deaa39c5dd6fc8855ed0}
``` 

<br>
  
</br>
 
![image](https://github.com/user-attachments/assets/4a124b8d-dfe1-4024-bcd4-7eab41767d40)

이렇게 풀이를 마칠 수 있었다.
