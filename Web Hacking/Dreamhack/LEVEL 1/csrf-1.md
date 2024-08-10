<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "csrf-1"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.08.01 풀이

![image](https://github.com/user-attachments/assets/83709c0a-602b-4684-b70d-661d09afbe45)
 
https://chs777888.tistory.com/47

에서 CSRF에 대해서, 학습한 후,

이를 실습으로 익히기 위해, Dreamhack 워게임에 있는 1단계의

csrf-1을 풀어보려고 한다. 

<br>

</br> 

![image](https://github.com/user-attachments/assets/5ee46310-ce3b-4a64-9677-04dfe531b2e0)

서버에 우선 들어가보았다. 

<br>
</br> 

![image](https://github.com/user-attachments/assets/b5fe776b-a5f4-4aab-9f87-5c68da6092b1)

처음 vuln 페이지에 접속해보니, <*>alert(1) 가 화면에 출력된 것을 발견하였다. 파라미터에도 script 구문으로 <script>alert(1)</script>라고 표기되어 있는 것을 확인할 수 있다.

 <br>
 
</br> 

![image](https://github.com/user-attachments/assets/07fe605a-2c36-4e82-9ab9-b67bd4ecbc74)

memo 페이지에 접속해보니, 지난번에 푼 xss 워게임과 유사해보였다. 파라미터에 memo?memo=hello라고 표기되어 있는 것을 보니, memo 페이지에 접속하면, memo 파라미터로 hello를 전달하는 것 같다. 지금 내가 memo 페이지에 2번째로 접속해서, 화면에 hello가 두 번 출력되어 있는 것 같다.

 <br>
 
</br> 
 
![image](https://github.com/user-attachments/assets/7d6e8f6c-3c19-43f8-8b5b-cc174030bc93)

notice_flag 페이지는 접속해보니, Acess Denied 문구가 출력되었다.

 <br>
 
</br> 

![image](https://github.com/user-attachments/assets/16da08aa-e2ce-45ff-b98d-62397afb85f9)

flag 페이지는 입력칸이 있는 것을 확인할 수 있다. 뒷부분은 내가 직접 입력해야되지만, 앞부분 경로를 살펴보니, 맨 처음에 살펴본, vuln 페이지 경로인 것 같다. 

이제 코드를 살펴보자.

 <br>
 
</br>  

![image](https://github.com/user-attachments/assets/b9a597a0-f112-488c-8a58-5840ae777a3d)

![image](https://github.com/user-attachments/assets/c2d82e74-0817-4921-a8bf-3c0d3257f273)

![image](https://github.com/user-attachments/assets/dd70f78b-77f0-4787-8ea1-4cdf06e753ec)

 <br>
 
</br>  

## vuln 페이지
![image](https://github.com/user-attachments/assets/99afe616-6655-4fab-89fb-647d576bcc2d)

vuln 페이지 부분 코드를 살펴보니, 파라미터로 전달된 값에서 "frame", "script", "on" 을 *로 대체하는 내용이 있는 것을 알 수 있다. 이런 필터링이 있으니, xss 공격은 어려울 것으로 보인다. 그래서, vuln 페이지에 script 구문이 *로 대체되어서, <*>alert(1) 로 출력되는 건가? 라고 생각했다.

 <br>
 
</br>

## notice_flag 페이지
![image](https://github.com/user-attachments/assets/8a44e511-4a23-4d10-873f-0bdac2cc2834)

notice_flag 페이지를 살펴보니, "127.0.0.1" 인 해당 아이피가 아니면, 아까 보았단 Acess Denied가 출력되고, userid값이 admin이 아니면 Acess Denied2를 출력하는 내용이다. 만약, 다음 조건식들에서 문제가 없으면, memo_txt 값에 flag 값이 적혀지는 코드인 것을 확인할 수 있다.

<br>

</br> 

## memo 페이지
![image](https://github.com/user-attachments/assets/e4873bde-f6c9-45d1-b940-d2fe96d37a4f)

memo 페이지 부분 코드를 살펴보니, 아까 본 memo_txt가 memo 페이지에서 출력되는 내용이다. 

<br>
</br> 

그러면, 아까 flag 페이지에 있는 입력칸에다가 notice_flag 페이지에 이동하게 하고, 여기서 userid가 admin이라고 지정해주는 내용을 입력하면 될 것 같다고 생각했다. 

script 구문 대신 img 태그를 사용하여

<br>
</br> 

```
<img src="/admin/notice_flag?userid=admin">
```
이라고 작성해보았다. 

이를 flag 페이지에 입력해보니,

<br>

</br> 

 ![image](https://github.com/user-attachments/assets/a9fc718b-4634-4bd2-8984-8d7af89baea4)

![image](https://github.com/user-attachments/assets/4da89059-6447-420c-b996-f2b21ca69d7d)

good 알림창이 뜨고,

memo 페이지로 이동해보니,

<br>
</br> 
 
![image](https://github.com/user-attachments/assets/fb993450-b978-4877-b3c3-081021d55ecd)

flag값이 출력된 것을 확인할 수 있다. 

 <br>
 
</br> 

```
DH{11a230801ad0b80d52b996cbe203e83d}
```

 <br>
</br> 
 
![image](https://github.com/user-attachments/assets/521c7277-2f95-4711-9b53-2eec09a1d255)

이렇게 문제를 해결할 수 있었다.
