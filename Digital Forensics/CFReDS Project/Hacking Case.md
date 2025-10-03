<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> CFReDS Project "Hacking Case" Write-UP</h1>
</body>
<br>
<br>
</html>

2025.05.20 풀이

<img width="1280" height="748" alt="image" src="https://github.com/user-attachments/assets/0113d917-c8e3-44d7-9975-e336ea158b85" />

FTK Image 로 SAM, SYSTEM, SOFTWARE 하이브를 추출해주었다.

<br>

</br>

### 1. What is the image hash? Does the acquisition and verification hash match?

<img width="746" height="1138" alt="image" src="https://github.com/user-attachments/assets/9083f9a0-8978-4c2e-adaf-ac9a870d1ac3" />

리눅스에서 LOG 파일을 출력하니, 각 파일의 MD5 해시값을 확인할 수 있다.


<img width="962" height="622" alt="image" src="https://github.com/user-attachments/assets/8bb7faa2-c033-49be-9817-39d30143bf85" />

<img width="984" height="720" alt="image" src="https://github.com/user-attachments/assets/d2cf3002-e2b3-46e4-93e8-54e36c21b985" />

파일을 FTK Image의 Verify Image 기능을 통해 MD5 값 일치하는 것을 확인할 수 있었다.

<br>

</br>

### 2. What operating system was used on the computer?

<img width="1280" height="773" alt="image" src="https://github.com/user-attachments/assets/461a7949-f3e6-4344-8072-04659bbcf3f3" />

컴퓨터에서 사용하는 운영체제를 찾기 위해, `SOFTWARE\Microsoft\Windows NT\CurrentVersion` 경로에서 Miscrosoft Windows XP 인 것을 알아낼 수 있었다.

<br>

</br>

### 3. When was the install date?

<img width="1280" height="814" alt="image" src="https://github.com/user-attachments/assets/6249b0c9-748e-4a98-b38b-8a779b744373" />

운영체제를 설치한 날짜를 확인하기 위해, SOFTWARE\Microsoft\Windows NT\CurrentVersion 경로에서 InsallDate를 보니, 1092955707 값을 확인할 수 있었다. 이를 16진수(리틀 엔디안)으로 변경하여, 0x3B2E254 값을 얻었다.



<img width="1280" height="630" alt="image" src="https://github.com/user-attachments/assets/b0ed9669-d2da-45b0-aac2-fe0f9af59050" />

이를 DCode 프로그램을 통해, 디코딩하니, 2004년 8월 19일 목요일 22:48:27 인 것을 알아낼 수 있었다

`UTC+0 : Thu, 19 August 2004 22:48:27`

<br>

</br>

### 4. What is the timezone settings?

<img width="1280" height="630" alt="image" src="https://github.com/user-attachments/assets/6d9cc2e4-6a2b-4665-816e-6aa6dab7e54e" />

해당 컴퓨터의 타임존이 어떻게 설정되어있는지를 묻는 문제이다. 이는 `SYSTEM\ControlSet001\Control\TimeZoneInformation` 경로에서 
ActiveTimeBias 를 확인하니, 300이라는 값을 얻을 수 있었는데 300은 5시간이므로 UTC -5 의 타임존을 가지고 있는 것을 알 수 있다.

<br>

</br>

###  5. Who is the registered owner?

<img width="1280" height="728" alt="image" src="https://github.com/user-attachments/assets/f2c9d604-4a84-4862-a675-2e72efe2db91" />

registered owner가 누구인지 알아내는 문제이다. 이는 SOFTWARE\Microsoft\Windows NT\CurrentVersion 경로에서 
RegisteredOwner를 통해 알 수 있었다. 답은 Greg Schardt 이다.

<br>

</br>

### 6. What is the computer account name?

<img width="1280" height="769" alt="image" src="https://github.com/user-attachments/assets/dce2f8bf-e24b-4b2b-91d0-fee86da57093" />

이는 컴퓨터 계정 이름을 묻는 문제로, SYSTEM\ControlSet001\ComputerName\ComputerName 경로에서
ComputerName 창의 값을 통해, 알아낼 수 있었다. 답은 N-1A9ODN6ZXK4LQ이다.

<br>

</br>

### 7. What is the primary domain name?

<img width="1280" height="667" alt="image" src="https://github.com/user-attachments/assets/c9a37b2a-6847-4051-8472-64c986f80afb" />

기본 도메인을 찾는 문제로, SYSTEM\ControlSet001\Services\Tcpip\Parameters 경로에서
Domain 창을 확인해보니, 아무 값도 나와있지 않았다. 애초에 등록되어 있지 않은건지, 내가 찾지 못한 건지 잘 모르겠다..

<br>

</br>

### 8. When was the last recorded computer shutdown date/time?

<img width="1280" height="655" alt="image" src="https://github.com/user-attachments/assets/4df0215f-be70-431e-95b5-43970b4c696c" />

마지막으로 컴퓨터가 꺼진 시간과 날짜를 묻는 문제이다. SYSTEM\ControlSet001\Control\Windows 경로에서 ShutdownTime 값을
확인하니, C4FC00074D8CC401 값을 얻을 수 있었다. 



<img width="1280" height="713" alt="image" src="https://github.com/user-attachments/assets/ce62e8cb-c1b8-47ff-a6b2-fee1a73d5467" />

이를 DCode를 통해 살펴보니, 2004년 8월 27일 금요일 15:46:33 인 것을 알아낼 수 있었다.

`UTC+0 : Fri, 27 August 2004 15:46:33`


<br>

</br>

### 9. How many accounts are recorded (total number)?

<img width="1280" height="600" alt="image" src="https://github.com/user-attachments/assets/95bbf8d8-e39d-47af-b025-f9bedebabde0" />

이는 계정의 개수를 찾는 문제이다. 

SAM\SAM\Domains\Account\Users  해당 경로를 들어가니, 표가 나오는데

해당 표를 살펴보면, 총 5개의 계정이 있는 것을 확인할 수 있다.


<br>

</br>

### 10. What is the account name of the user who mostly uses the computer?

<img width="1280" height="328" alt="image" src="https://github.com/user-attachments/assets/8129062b-b66d-4ebc-ac6d-796f93b38a4e" />

이는 컴퓨터를 주로 사용하는 사용자의 계정 이름을 찾는 문제이다!
바로 위 문제와 같은 경로에 있는 표에서 Total Login 값을 보니,
Mr.Evil만 로그인 기록이 15번인 것을 확인할 수 있다.


<br>

</br>

### 11. Who was the last user to logon to the computer?

바로 위 문제에서 알 수 있듯이 총 5개의 계정 중 로그인한 사람은 Mr.Evil밖에 없기 때문에

마지막으로 로그인한 사람도 Mr.Evil 이다.

