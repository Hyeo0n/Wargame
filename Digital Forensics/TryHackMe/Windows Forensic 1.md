<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> TryHackMe "Window Forensics 1" Write-UP</h1>
</body>
<br>
<br>
</html>

2025.05.20 풀이


## Task2 : Windows Registry and Forensics
<img width="666" height="1216" alt="image" src="https://github.com/user-attachments/assets/efc49aad-0b7b-48ff-adf0-1e3b178e0350" />

What is the short form for HKEY_LOCAL_MACHINE?

HKEY_LOCAL_MACHINE의 약어는 무엇인가?

```
답 :  HKLM
```

<br>

</br>

<img width="1214" height="218" alt="image" src="https://github.com/user-attachments/assets/a54547f8-16d6-4d83-93a8-bd2a29673f50" />

해당 키는 때때로 HKLM으로 약칭된다고 한다. 

<br>

</br>
<br>

</br>

### [관련 문서화]

1. Windows Registry

    : 시스템 설정 및 구성 정보를 저장하는 데이터베이스 모음

 

2. Registry 구조

    - Keys (레지스트리 키) : 트리 구조의 폴더처럼 계층적으로 구성됨

    - Values (값) : 각 Key에 저장되는 실제 데이터

 

  ☆ 레지스트리 하이브

      : 키, 서브키, 값 등을 포함한 하나의 큰 단위

        → 디스크 상에 단일 파일 형태로 저장됨

 

3. 5개의 주요 루트 키
   
    HKEY_CURRENT_USER :	현재 로그인한 사용자의 개인 설정(프로필, 폴더, 바탕화면, 제어판 설정 등)

    HKEY_USERS : 모든 사용자 계정의 프로필 정보 / HKEY_CURRENT_USER가 해당 서브키.

    HKEY_LOCAL_MACHINE : 시스템 전체에 영향을 주는 설정(하드웨어, 드라이버, OS 정보 등)

    HKEY_CLASSES_ROOT	: 파일 확장자나 COM 객체 등 파일 열기/연결 방식 정보

      → 시스템과 사용자 클래스 설정이 병합되어 표시됨

    HKEY_CURRENT_CONFIG	: 시스템 부팅 시점의 하드웨터 구성 정보 (현재 컴퓨터의 설정만 포함함)

<br>

</br>
 

  ① HKEY_CURRENT_USER (HKCU)

      : 현재 로그인한 사용자에 대한 개인 설정을 저장하는 루트 키

        ex) 바탕화면, 마우스/키보드 설정, 윈도우 탐색기 설정, 최근 사용한 문서 목록

  ② HKEY_USERS (HKU)

      : 모든 사용자 계정에 대한 설정 정보를 담는 루트 키

        ex) 모든 사용자 계정의 설정 정보, 로그인한 적 있는 사용자들의 데이터

        ☆ HKCU는 현재 사용자  SID를 가리키는 심볼릭 링크 역할을 함

  ③ HKEY_LOCAL_MACHINE (HKLM)

      : 컴퓨터 자체에 대한 전역 설정 정보가 저장

        → 이곳에 HARDWARE, SAM, SECURITY, SOFTWARE, SYSTEM과

            같은 주요 하이브들이 있음

        ex) 하드웨어 설정, 장치 드라이버, 시스템 서비스, 보안 설정,

              설치된 소프트웨어 및 라이센스 정보

  ④ HKEY_CLASSES_ROOT (HKCR)

      : 파일 확장자와 연결된 기본 프로그램 정보 저장

        ex) .txt - 메모장, .html - 브라우저 

      ● 실제로 HKEY_LOCAL_MACHINE\Software\Classes와
         HKEY_CURRENT_USER\Software\Classes를 병합한 

      ● 동일한 키가 존재할 경우 HKCU를 우선 적용함

  ⑤ HKEY_CURRENT_CONFIG (HKCC)

      : 시스템 부팅 시 현재 하드웨어 설정 정보 포함

        → 하드웨어 프로파일에 따라 달라질 수 있음

        => 동적인 구성요소, HKLM의 하위 키

             SYSTEM\CurrentControlSet\Hardware Profiles\Current의 뷰

        ex) 디스플레이 해상도, 프린터 구성, 오디오 장치 설정

<br>

</br>

## Task3 : Accessing registry hives offline
<img width="640" height="1418" alt="image" src="https://github.com/user-attachments/assets/36f3c685-9c71-4f87-84ff-160515417d9f" />

What is the path for the five main registry hives, DEFAULT, SAM, SECURITY, SOFTWARE, and SYSTEM?  

DEFAULT, SAM, SECURITY, SOFTWARE, SYSTEM 등 5개 주요 레지스트리 하이브의 경로는 무엇인가?


```
답 : C:\Windows\System32\Config
```

<br>

</br>

<img width="1280" height="338" alt="image" src="https://github.com/user-attachments/assets/347af0f4-345e-4666-af62-fdb4cb258f67" />

5개의 주요 레지스트리 하이브는 C:\Windows\System32\Config에 위치한다고 한다.


<br>

</br>

What is the path for the AmCache hive?

AmCache 하이브의 경로는 무엇인가?

```
답 : C:\Windows\AppCompat\Programs\Amcache.hve
```

<br>

</br>

<img width="1280" height="122" alt="image" src="https://github.com/user-attachments/assets/e7b75b0f-b854-4d74-92f3-0bac8a9fb763" />

Amcache 하이브는 C:\Windows\AppCompat\Programs\Amcache.hve 경로에 위치해 있다고 한다. 


<br>

</br>


### [관련 문서화]
1. 레지스트리 하이브 접근

     : 파일 시스템에 접근 가능한 경우, regedit.exe 이용 가능

      → 디스크 이미지만 접근 가능한 경우, 디스크에 저장된 하이브 파일의 실제 위치를 알아야 함

 

2. 주요 레지스트리 하이브의 디스크 상 위치

   - 하이브 파일 디렉토리 경로 : C:\Windows\System32\Config\

      ● DEFAULT : HKEY_USERS\DEFAULT

      ● SAM : HKEY_LOCAL_MACHINE\SAM

      ● SECURITY : HKEY_LOCAL_MACHINE\Security

      ● SOFTWARE : HKEY_LOCAL_MACHINE\Software

      ● SYSTEM : HKEY_LOCAL_MACHINE\System

     => 주로 시스템 전역 설정 및 계정 정보 포함

 

3. 사용자 정보가 포함된 추가 하이브

    - 형식 : "C:\Users\<사용자명>\"

      → 사용자 프로필 디렉토리      ● NTUSER.DAT : HKEY_CURRENT_USER (로그인 시 마운트)
      ● USRCLASS.DAT : HKEY_CURRENT_USER\Software\Classes

 

      ☆ NTUSER.DAT (숨김 파일)

          : 사용자별 바탕화면 설정, 최근 실행한 프로그램, 애플리케이션 설정 

 

      ☆ USRCLASS.DAT (숨김 파일)

          : 주로 파일 확장자 연결 설정, 클래스 정보, 최근 사용 기록

          - 경로 : C:\Users\<사용자명>\AppData\Local\Microsoft\Windows\

 

4. AmCache Hive

   : 최근 실행된 프로그램에 대한 정보 저장

    → 이를 통해 프로그램 호환성, 최적화 정보를 관리

   - 경로 : C:\Windows\AppCompat\Programs\Amcache.hve

 

5. 트랜젝션 로그 (Transaction Logs) & 백업(Backups)

 

  ☆ 트랜잭션 로그 (Transaction Logs)

    : 데이터를 쓸 때 변경 내용을 로그 파일에 먼저 저장

      → 레지스트리 하이브의 변화 이력을 기록

        => 하이브에 반영되지 않은 최신 변경 내용이 있을 수도 있어서

             포렌식 분석 시 함께 확인해야 함

 

  ☆ 레지스트리 백업 (Registry Backups)

      : 하이브 파일 전체를 백업한 버전

        - 경로 : C:\Windows\System32\Config\RegBack

          → 해당 디렉토리에 10일마다 레지스트리 백업 생성

       => 누군가 레지스트리 키를 삭제/변조한 경우,

            백업 디렉토리를 토해 이전 상태 복원 가능
