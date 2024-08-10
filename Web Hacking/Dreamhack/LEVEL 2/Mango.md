<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "Mango"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.08.02 풀이

![image](https://github.com/user-attachments/assets/c70fcd76-9daf-46dd-98b6-7ce072242cdd)

https://chs777888.tistory.com/50

에서 NoSQL Injection에 대해서, 학습한 후,

이를 실습으로 익히기 위해, Dreamhack 워게임에 있는 2단계의

Mango를 풀어보려고 한다. 

 <br>
 
</br>

![image](https://github.com/user-attachments/assets/faf0f572-b82d-4ad6-a622-33e15cbf2950)

우선 서버에 접속해보니, 파라미터같이 생긴 것이 화면에 출력되었다. 이를 url에 입력해보니,

<br>

</br>

![image](https://github.com/user-attachments/assets/3c4cb7f8-aabc-4525-977e-7dc966f8b70a)

guest가 출력되는 것을 확인할 수 있다. 그 후, admin-DH{32alphanumeric} 을 활용해서도 url에 입력해보았다.

<br>

</br> 

![image](https://github.com/user-attachments/assets/a76d6d3f-f877-446e-a53f-f5860dd41f2d)

이번에는 filter가 출력되었다.

이제 코드를 살펴보려고 한다. 

<br>

</br> 

![image](https://github.com/user-attachments/assets/f18db506-c3e8-488f-945b-64cd7138498c)

![image](https://github.com/user-attachments/assets/51ebc218-49f6-4ad0-9ec7-989872f4396c)

<br>

</br> 
<br>

</br> 
<br>

</br>  

![image](https://github.com/user-attachments/assets/f6aa0d8c-ecc4-4064-9008-603650a3b7a2)
 
해당 부분을 살펴보니, admin, dh, admi 이라는 문자열이 있으면, flag 변수를 true로 설정하는 것을 알 수 있다.

<br>

</br>  

![image](https://github.com/user-attachments/assets/cb17b6fb-54ca-4a09-ac7e-622050a85876)

코드에 서버에서는 발견하지 못했던 login 페이지를 발견했다. red.query를 위에서 본 filter 함수에 전달하여, 금지되는 문자열을 발견하면, filter를 출력하는 것을 볼 수 있다. 그리고, 이 red.query에서 uid와 upw를 빼와서, DB의 user 컬렉션에서 uid와 upw가 일치하는 문서를 찾는다. 결과를 찾으면 uid를 클라이언트에게 보내는 내용이다. 쿼리 변수 타입을 검사하지 않기 때문에 NoSQL 인젝션을 활용하면 될 것 같다.

아까 /login?uid=admin&upw=DH{32alphanumeric} 를 입력했을 때, filter로 출력되니까, 필터링되지 않게, 정규표현식을 임의로 조금 수정해야 겠다.

$regex 연산을 사용해서, 데이터를 검색해보도록 하겠다.

<br>

</br>  

```
 /login?uid[$regex]=ad.in&upw[$regex]=D.{*
```

<br>
</br>   

작성한 정규 표현식을 입력해보니, 

![image](https://github.com/user-attachments/assets/d576de28-2aac-4660-9200-306bc1fa9bc7)


필터링되지 않고, admin이 출력된 것을 확인할 수 있다. 

이제 정규 표현식을 사용해서, admin의 upw를 알아낼 코드를 작성해보도록 하겠다. 

<br>

</br> 

![image](https://github.com/user-attachments/assets/86be1bd9-cdbf-41b5-bb2d-b2430b7a78fe)

url= 으로 공격할 URL 주소를 설정해놓고, DH{32alphanumeric} 인 걸로 봐선, flag가 32자리로 추정되기 때문에, for문을 사용해서 32번 반복하게끔 설정해놓았다. flag_set 의 문자를 하나씩 대입하여 서버에 요청을 보내고, 이 응답이  admin일 경우, 이를 flag에 하나씩 추가하게끔 설정해놓았다. 

이를 실행해보니,

<br>

</br>  

![image](https://github.com/user-attachments/assets/1d989355-e862-4689-8334-5284352c8ad4)

flag값을 알아낼 수 있었다. 

<br>

</br>  


```
DH{89e50fa6fafe2604e33c0ba05843d3df}
``` 

<br>

</br>  
 
![image](https://github.com/user-attachments/assets/1e7f8bf4-fea1-4717-a404-c20b04f3234e)

이렇게 문제를 풀이해낼 수 있었다.
