<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> TryHackMe "Investigating Windows" Write-UP</h1>
</body>
<br>
<br>
</html>

2025.05.20 풀이

<img width="1280" height="687" alt="image" src="https://github.com/user-attachments/assets/f1cfd652-2782-4ccd-94b6-b5e818b815a7" />

<br>

</br>

### Q1. Whats the version and year of the windows machine?

<img width="832" height="190" alt="image" src="https://github.com/user-attachments/assets/609ce568-321f-43dc-83bc-d549d1b652a4" />

해당 컴퓨터의 버전과 연도를 확인하기 위해, System Information 을 통해, 알아낼 수 있었다.

`Windows Server 2016`


<br>

</br>

### Q2. Which user logged in last?

<img width="1038" height="1212" alt="image" src="https://github.com/user-attachments/assets/ff991e28-91ed-4066-b388-9f8bc2aedf26" />

 이벤트 뷰어에서, ID가 4624인 로그인 이벤트만 필터링 설정한다.


 <img width="1280" height="1262" alt="image" src="https://github.com/user-attachments/assets/8d76a5bd-8ab2-4031-9f95-a16e4b26bbf5" />

그 후, 이를 날짜와 시간별로 정렬하니, 가장 마지막 로그인 사용자는 Adiministrator인 것을 확인할 수 있다.

`Adiministrator`


<br>

</br>

### Q3. When did John log onto the system last? Answer format: MM/DD/YYYY H:MM:SS AM/PM

<img width="1082" height="454" alt="image" src="https://github.com/user-attachments/assets/529b95f0-4ccf-4c61-bb6e-6e087cc8d067" />

바로 이어서, John을 검색하니 John과 관련된 로그인 기록들을 걸러낼 수 있었다.


<img width="1250" height="1308" alt="image" src="https://github.com/user-attachments/assets/22a5e6a1-9cd9-4bb8-b925-f30c88d7e25f" />

이를 살펴보니, 존의 마지막 로그인 시각은 2019년 3월 2일 오후 5시 48분 32초이다.

`03/02/2019 5:48:32 PM`


<br>

</br>

### Q4. What IP does the system connect to when it first starts?

<img width="822" height="626" alt="image" src="https://github.com/user-attachments/assets/26272d29-38e6-4867-be1d-0253d0244b6c" />

System Information으로 들어가서, Software Environment - Startup Programs창으로 들어가니 처음 시작될 때 연결되는 IP 주소를 발견할 수 있었다.

`10.34.2.3`


<br>

</br>

### Q5. What two accounts had administrative privileges (other than the Administrator user)?  Answer format: username1, username2

<img width="1092" height="420" alt="image" src="https://github.com/user-attachments/assets/b432fff4-cf05-4e01-b7a8-9b22a4284ddb" />

Windows Powershell로 들어가서, net localgroup "Administrators" 명령어를 입력하니, 관리자 권한을 가진 계정을 출력할 수 있었다.

`Guest, Jenny`


<br>

</br>

### Q6. What’s the name of the scheduled task that is malicous.

<img width="1258" height="912" alt="image" src="https://github.com/user-attachments/assets/5acaa000-976b-431d-b993-3a1f78e6d885" />

Task Scheduler을 통해, Task Scheduler Library를 누르니, 여러 작업들이 나온다. 예약된 작업들 중 일반적인 것과 달리 의심스러운 Clean file system을 발견할 수 있엇다.

`Clean file system`


<br>

</br>

###  Q7. What file was the task trying to run daily? & Q8. What port did this file listen locally for?

<img width="1080" height="818" alt="image" src="https://github.com/user-attachments/assets/d4096285-5519-4844-a249-173591ec92c8" />

위에서 발견한 Clean file system을 클릭하니, 속성 창이 나오는데, 이곳에서 Actions창에 들어가니, 어떤 파일을 매일 실행하고, 어떤 포트로 로컬로 수신하고 있는지 확인할 수 있었다.

`nc.ps1`

`1348`


<br>

</br>

### Q9. When did Jenny last logon? & Q10. At what date did the compromise take place? Answer format: MM/DD/YYYY

<img width="732" height="512" alt="image" src="https://github.com/user-attachments/assets/87bb4464-278c-4eed-b054-df3cedd0d0e0" />

Windows Powershell에서 net user Jenny 명령어를 입력하니, 사용자의 마지막 로그온 정보를 얻을 수 있었다

`Never`

<br>

</br>
또한, 사용자가 생성된 날짜를 통해, 날짜를 추정할 수 있다.

`03/02/2019`


<br>

</br>

### Q11. During the compromise, at what time did Windows first assign special privileges to a new logon? Answer format: MM/DD/YYYY HH:MM:SS AM/PM

<img width="1192" height="1154" alt="image" src="https://github.com/user-attachments/assets/ac1a828b-899e-4a71-b9fa-3379786024ab" />

침해당할 당시, 특수 권한을 할당받은 시간을 찾기 위해, 이벤트 뷰어에서 위에서 찾은 날짜를 기준으로 필터링을 해주었다.



<img width="1130" height="1054" alt="image" src="https://github.com/user-attachments/assets/0bb7c2c8-eaa1-4b72-b186-a82713e06f25" />

여러 로그들이 나왔는데, 제공된 문제에서 주어진 힌트인 0:00:49 PM을 통해, 정확한 시간을 알아낼 수 있었다.

`03/02/2019 4:04:49 PM`


<br>

</br>

### Q12. What tool was used to get Windows passwords?

<img width="684" height="480" alt="image" src="https://github.com/user-attachments/assets/e06a5f94-0797-4446-bc5e-f91446dd197b" />

비밀번호를 알아내는 도구라는게 악성행위를 하는 도구를 알아내라고 하는 것 같은데, 계속 위에 문제를 진행하면서,
C:\TMP 경로로 cmd 창이 계속 뜨면서 버벅거렸었다. 이 부분이 의심스러워 해당 경로로 가서 mim-out이라는 파일을 열어보니, 
자격 증명을 얻어내는 도구인 Mimikatz를 발견할 수 있었다.

`Mimikatz`


<br>

</br>

### Q13. What was the attackers external control and command servers IP?

<img width="682" height="752" alt="image" src="https://github.com/user-attachments/assets/2c3730bb-43b3-49fb-a622-653834c2317b" />

공격자의 외부 제어 및 명령 서버 IP를 알아내기 위해, C:\Windows\System32\drivers\etc 경로에 있는 hosts 파일을 확인해보았다.
이 파일을 로컬 DNS 역할을 하여, 로컬 컴퓨터에서 호스트 이름을 IP 주소에 매핑할 수 있도록 사용되는 파일이다. 
이를 살펴보니, google.com IP들이 다른 IP들과 달리, 할당된 IP 주소를 사용하지 않는 것을 발견할 수 있었다.

`76.32.97.132`


<br>

</br>

### Q14. What was the extension name of the shell uploaded via the servers website?

<img width="774" height="570" alt="image" src="https://github.com/user-attachments/assets/23495d82-d072-4414-ae48-2b8192237371" />

서버 웹사이트를 통해 업로드된 쉘의 확장자를 알아내기 위해, Windows용 일반 웹 서버인 Microsoft Internet Information Services의
웹 서버 로그 파일이 있는 곳인 C:\inetpup로 이동하여 살펴보았다. JSP 파일 2개와 GIF 파일 1개가 있었는데, 이를 각각 실행해보니, tests.jsp 파일이 cmd 를 여는 쉘인 것을 알아낼 수 있었다.


<br>

</br>

### Q15. What was the last port the attacker opened?

<img width="744" height="664" alt="image" src="https://github.com/user-attachments/assets/f3659b84-0c88-4035-a7e7-a14b99d69c17" />

공격자가 마지막으로 연 포트를 확인하기 위해, 방화벽 설정하는 곳으로 들어갔다. Inbound Rules를 선택하고, Allow outside connections for development Properties 규칙을 살펴보았다.
해당 규칙은 주로 개발 목적으로 외부 연결을 허용하는 규칙으로, 이는 공격자가 해당 시스템에 연결할 수 있도록 많이 사용된다고 한다.
이를 통해 살펴보니, 1337 포트 번호를 발견할 수 있었다.

`1337`


<br>

</br>

### Q16. Check for DNS poisoning, what site was targeted?

<img width="682" height="752" alt="image" src="https://github.com/user-attachments/assets/1d055f6c-18a0-4b6f-9af4-bcf25c24a378" />

이는 위에서 이미 확인하였는데, 공격자는 할당되지 않은 IP로 google.com 사이트를 사용하였다.
이를 통해, google.com이 타켓 사이트인 것을 알 수 있다.

`google.com`


<br>

</br>


