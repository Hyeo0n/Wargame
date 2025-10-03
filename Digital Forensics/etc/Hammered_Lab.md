<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> CyberDefenders "Hammered Lab" Write-UP</h1>
</body>
<br>
<br>
</html>

2025.05.10 풀이

<img width="1148" height="1588" alt="image" src="https://github.com/user-attachments/assets/9985a578-e97a-4818-a3ed-d709ab41acd5" />



<br>

</br>

### 질문 1. 공격자는 어떤 서비스를 사용하여 시스템에 접근하였는가?
<img width="932" height="350" alt="image" src="https://github.com/user-attachments/assets/4189d6ad-cabc-4f6a-8271-2f78d952d113" />

<img width="1252" height="1154" alt="image" src="https://github.com/user-attachments/assets/851ea81a-5826-493f-86bb-c1dd01466283" />

auth.log 파일을 출력해서 살펴보니, 수많은 실패한 로그인 시도를 살펴볼 수 있다.
이러한 로그는 sshd 서비스에서 생성되는데, 이는 SSH의 서버 프로그램이므로,
누군가 원격에서 이 컴퓨터에 접속하려고 할 때 사용하는 서비스는 ssh이다.

```
ssh
```
<br>

</br>

### 질문 2. 타겟 시스템의 OS 버전은 무엇인가?
<img width="936" height="340" alt="image" src="https://github.com/user-attachments/assets/4432c348-d11d-4881-ab16-c0997a5c508f" />

<img width="1280" height="394" alt="image" src="https://github.com/user-attachments/assets/26f7a8bb-36b4-46f1-b9f2-e89213955fdd" />

  dmesg 파일을 출력하니, 시스템 부팅 시 출력된 커널 메시지를 볼 수 있다.
 Linux Version 줄을 살펴보니, OS 버전을 살펴볼 수 있었다.

```
4.2.4-1ubuntu3
```
<br>

</br>

### 질문 3. 침해된 계정의 이름은 무엇인가?
<img width="936" height="306" alt="image" src="https://github.com/user-attachments/assets/9e145e40-0724-4b02-80f9-2672f5133aa7" />

<img width="1084" height="232" alt="image" src="https://github.com/user-attachments/assets/66bc001a-3b35-485b-9510-801d3b79d45a" />

auth.log 파일을 살펴보니, Accepted password for root 를 확인할 수 있다.

이는 공격자가 root 계정으로의 로그인 시도를 성공한 것을 알 수 있다

```
root
```
<br>

</br>

### 질문 4. 각 IP가 서로 다른 공격자를 나타낸다고 가정할 때, 시스템에 접근한 공격자는 몇 명인가?
<img width="968" height="404" alt="image" src="https://github.com/user-attachments/assets/fd6d1ee7-0147-466f-9d62-8fb949633bab" />

<img width="782" height="60" alt="image" src="https://github.com/user-attachments/assets/85aba12d-47b0-4f8a-8cb8-7ecd7f7f486e" />

<img width="880" height="476" alt="image" src="https://github.com/user-attachments/assets/f771c54f-06d6-4cab-ab6d-59646086689c" />

해당 명령어를 통해 auth.log 파일에서 root 계정에 성공적으로 로그인한

  IP 주소들을 추출한다. 그리고 이를 ips.txt에 저장한다.

  ips.txt를 살펴보니, 로그인 시도를 성공한 18개의 아이피가 추출되었다.
<br>

</br>
  
  <img width="1270" height="382" alt="image" src="https://github.com/user-attachments/assets/74ba4473-0649-4e7e-bb2f-21947673d218" />

그래서 각 IP가 공격자인지 확인하고 거르기 위해, 

그 IP가 authentication failure 로그에 포함되어 있는지 확인하는 

스크립트를 작성하였다.
해당 스크립트를 실행하면, 6개의 IP가 추출된다

```
6
```

<br>

</br>

### 질문 5. 어떤 공격자의 IP 주소가 시스템에 가장 많이 성공적으로 로그인했는가?
<img width="954" height="328" alt="image" src="https://github.com/user-attachments/assets/80101f86-d224-4a64-ad40-57dd0a701f84" />

아까 위에서 작성한 스크립트를 실행하면, IP : 실행된 횟수 형태로 출력된다.

이를 통해서 219.150.161.20 IP는 총 4회 로그인 성공으로 제일 많이 한 IP이다.

```
219.150.161.20
```

<br>

</br>

### 질문 6. Apache 서버로 얼마나 많은 요청이 전송되었는가?

<img width="954" height="336" alt="image" src="https://github.com/user-attachments/assets/d283fa7b-2171-4c28-a373-ec59995c9078" />

<img width="336" height="66" alt="image" src="https://github.com/user-attachments/assets/5e900da0-8601-4d7d-a713-3972aa8f63c0" />

 Apache 웹 서버가 받아낸 HTTP 요청의 총 개수를 살펴보려면,

웹 서버 액세스 로그 파일의 줄 수를 확인하면 된다.

www-access.log 파일의 줄 수를 출력하는 명령어를 통해 365인 것을 알 수 있다.

```
365
```

<br>

</br>

### 질문 7. 방화벽에 몇 개의 규칙이 추가되었는가?

<img width="950" height="334" alt="image" src="https://github.com/user-attachments/assets/71d817f6-f3fb-44f3-838b-d593e51d7029" />

<img width="1132" height="252" alt="image" src="https://github.com/user-attachments/assets/50e58793-0f1f-41b2-9f28-c88ab40f9d8a" />

auth.log 파일에서 ufw allow 명령을 찾아내어, 시스템에 추가된

 방화벽 허용 규칙의 개수를 알아낼 수 있다.

중복된 걸 제외하고 찾아보니, 총 6가지 서로 다른 규칙이 있다.

```
6
```

<br>

</br>

### 질문 8. 대상 시스템에 다운로드된 파일 중 하나가 스캐닝 도구인데, 이 도구의 이름은?

<img width="958" height="328" alt="image" src="https://github.com/user-attachments/assets/33fc3eaa-a5b2-40f6-82fb-c38bc539fb90" />

<img width="618" height="128" alt="image" src="https://github.com/user-attachments/assets/9c7cbd90-55cf-42c3-ac51-ab7fdf66abcd" />

 term.log 파일을 출력하여 살펴보니, 대표적인 네트워크 스캐닝 도구

nmap을 발견할 수 있었다.

```
nmap
```

<br>

</br>

### 질문 9. IP 주소 219.150.161.20을 이용한 공격자의 마지막 로그인은 언제였는가?

<img width="956" height="414" alt="image" src="https://github.com/user-attachments/assets/b20bbdf0-bde6-4a92-a00a-6ea15a4a9c3a" />

<img width="856" height="164" alt="image" src="https://github.com/user-attachments/assets/db35ba72-4c2f-4ab5-92f9-2eaec2f4d9b9" />

auth.log 파일에서 219.150.161.20이 포함됨과 동시에

그중에서도 "Accepted password for root"가 포함된 줄을

출력하는 명령어를 입력하였다. 

살펴보니 가장 마지막 줄이 최신 로그인 기록인 걸 확인할 수 있다.

```
2010-04-19 05:56
```

<br>

</br>

### 질문 10. 데이터베이스에 두 개의 경고 메시지가 표시되었는데, 가장 중요하고 위험한 메시지는 무엇인가?

<img width="970" height="326" alt="image" src="https://github.com/user-attachments/assets/524ef471-c23f-455a-9342-62956d85d5b9" />

<img width="1086" height="304" alt="image" src="https://github.com/user-attachments/assets/81cf9ad0-0691-4f0f-ab48-a325da7aba96" />

daemon.log에서 mysql과 관련된 것 중 warning이라는 문구가 뜬 부분만 출력하는 명령어를 통해, 
MYSQL 관련된 모든 경고 메시지를 뽑아내보았는데, 메시지들을 살펴보니, 가장 위험한 메시지는 WARNING: mysql.user contains 2 root accounts without password!로 MySQL 데이터베이스에 비밀번호 없는 루트 계정이 2개가 
존재한다는 메시지였다.

```
mysql.user contains 2 root accounts without password!
```

<br>

</br>

### 질문 11. 대상 시스템에 여러 계정이 생성되었는데, 4월 26일 04:43:15에 생성된 계정은 무엇인가?

<img width="948" height="326" alt="image" src="https://github.com/user-attachments/assets/b3bf3890-b3da-4e54-8f99-1390f615c0c1" />

<img width="1136" height="62" alt="image" src="https://github.com/user-attachments/assets/37ff917f-a93e-457b-ac06-c93b78cc234b" />

 auth.log 파일에서 grep을 사용하여, 해당 04월 26일 04:43:15에 계정이 생성된 로그 줄을 추출하였다. 해당 계정의 이름은 wind3str0y이다. 

```
wind3str0y
```

<br>

</br>

### 질문 12. 일부 공격자는 프록시를 사용하여 검사를 실행하는데, 이 프록시에서 사용하는 사용자 에이전트는 무엇인가?

<img width="926" height="368" alt="image" src="https://github.com/user-attachments/assets/ac57e2a5-bf6b-499f-812b-b7c96c9266b0" />

<img width="996" height="268" alt="image" src="https://github.com/user-attachments/assets/a45fb899-3e9f-47bf-b062-446a2e7d0162" />

Apache 웹 서버 접근 로그를 분석해서,
공격자가 프록시를 사용해 스캔할 때 사용한 User-Agent 문자열을
찾아야 한다. 따라서 www-access.log 에서 Apache 로그에 등장한 모든 고유한
User-Agent 목록을 추출하는 명령어를 입력하였다. 출력된 User-Agent 목록을 살펴보니, 확연히 공격 툴로 보이는 것을 발견하였다. pxyscand/2.1 는 공격자들이 프록시를 검색하고 정리하기 위해 사용하는 도구로 알려진다. 

```
pxyscand/2.1
```
