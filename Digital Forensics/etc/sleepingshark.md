<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> "sleepingshark" Write-UP</h1>
</body>
<br>
<br>
</html>

2025.04.04 풀이

<img width="1280" height="556" alt="image" src="https://github.com/user-attachments/assets/2596dd3d-0ada-458e-b1b5-a8b19553c4a3" />

해당 파일을 와이어샤크로 실행해보았다.

<br>

</br>

<img width="1280" height="536" alt="image" src="https://github.com/user-attachments/assets/d3b01eb7-bb05-495c-902c-8a8080a27132" />

<img width="1280" height="743" alt="image" src="https://github.com/user-attachments/assets/84cb763a-b780-41bf-b49a-f8f5e3990f24" />

우선적으로 http를 먼저 보니, flag 어쩌구 저쩌구 하는 수많은 POST 요청을 확인할 수 있었다.

<br>

</br>

<img width="1280" height="773" alt="image" src="https://github.com/user-attachments/assets/6013e90b-50e2-4de7-aaab-0829fcbf55a2" />

```
/?q=SELECT IF(ASCII(SUBSTRING((SELECT flag FROM s3cr3t LIMIT 1),35,1))=156, SLEEP(3), 0)
```

맨 처음 POST를 살펴보니 다음과 같은 내용이 있었는데, SELECT, FROM 이 있는 것을 보니 SQL 관련된 문제인가 생각하였다. 해당 쿼리를 URL Decode 해보니, 다음과 같이 해석되었다.

- SELECT flag FROM s3cr3t LIMIT 1 : s3cr3t 테이블에서 flag 열의 값을 1개 가져옴

- SUBSTRING((...), 35, 1) : 얻은 문자열의 35번째 문자 1개를 잘라냄

- ASCII(....) : 35번째 문자의 ASCII 값을 가져옴

- IF(... =156, SLEEP(3), 0) : 해당 문자의 ASCII 코드가 156이면 3초동안 sleep 실행 

 

이런 비슷한 형식이 수도 없이 많이 반복되는 것을 보아, flag 열의 값 중에 N 번째 값을 추출해내는 게 반복되는 것처럼 보인다. 이러한 무한 반복을 직접 노가다로 하기에는 어려움이 크니 파이썬 코드를 작성해서 flag 값을 추출해보려고 한다.
<br>

</br>

(https://velog.io/@yeon_ni/Dreamhack-sleepingshark)
파이썬 코드를 혼자 쓰니, 수도 없이 오류가 나서 위에 라이트업을 참고해서 코드를 작성해보았다.

<br>

</br>

```
import dpkt
import datetime
from urllib.parse import unquote

def analyze_packets(pcap):
    flag_dict = {}
    post_payload = ''
    request_time = 0

    for timestamp, buf in pcap:
        try:
            eth = dpkt.ethernet.Ethernet(buf)
            ip = eth.data
            tcp = ip.data

            
            if not isinstance(ip, dpkt.ip.IP) or not isinstance(tcp, dpkt.tcp.TCP):
                continue

            
            if tcp.dport == 80 and tcp.data:
                try:
                    http = dpkt.http.Request(tcp.data)
                    if http.method == 'POST':
                        post_payload = unquote(http.uri)
                        request_time = timestamp
                except:  
                    continue

            
            elif tcp.sport == 80 and tcp.data and request_time and timestamp - request_time >= 3:
                try:
                    dpkt.http.Response(tcp.data)  
                    idx = int(post_payload.split('LIMIT 1),')[1].split(',')[0]) - 1
                    char = chr(int(post_payload.split('))=')[1].split(',')[0]))
                    flag_dict[idx] = char
                    print(f"Found flag[{idx}] = {char}")
                except:
                    continue
                request_time = 0  

        except Exception as e:
            print(f"Packet error: {e}")
            continue

    return ''.join(flag_dict.get(i, '_') for i in range(max(flag_dict.keys()) + 1))

def test():
    file_path = "C:/Users/82104/Downloads/디지털포렌식 3주차 과제/디지털포렌식 3주차 과제/sleepingshark/dump.pcap"
    try:
        with open(file_path, 'rb') as f:
            pcap = dpkt.pcap.Reader(f)
            flag = analyze_packets(pcap)
            print(f"\n Final Flag: {flag}")
    except FileNotFoundError:
        print("PCAP 파일 없음.")

if __name__ == "__main__":
    test()
```

참고한 코드를 조금 더 간결하고 효율적으로 바꿔서 위처럼 작성해보았다. 

일단 원본에 post_sent 코드 부분을 request_time 하나로 상태 추적을 단순하게 적성해보았다. 그래서 request_time이 0보다 크면 POST가 온 걸로 인식하게끔 바꿔보았다.

그 다음, 원본에 print(f"-- Request..()")  부분을 생략해서 여러 시간 출력과 디버깅 메시지 출력을 제거하였다.

idx_start ~ char_start 부분에서 find()를 사용하지 않고 split()를 사용하여 조금 더 짧게 코드를 작성해보았다. 

예외 오류가 너무 많이 나서, 예외 처리 방식 부분을 except Exception as e: 로 수정하여, 모든 예외를 하나로 받고 로그 찍고 무시하여, 오류가 덜 발생하고, 빠르게 진행되게끔 수정하였다.

 

코드를 간단히 설명하자면, 일단 dpkt 라고 pcap 파일 안의 패킷을 쉽게 분석해주는 파이썬 라이브러리를 새롭게 사용하였다. 그 후, URL 디코딩을 위해 upquote 라이브러리도 추가적으로 사용해주었다.

analyze_packets(pcap) 함수는 flag_dict 딕셔너리를 활용하여, 인덱스를 키로 추출된 문자를 하나씩 저장하게끔 하였다. 그 후, ` eth = dpkt.ethernet.Ethernet(buf)  ip = eth.data  tcp = ip.data ` 를 활용하여, 패킷 안에 담긴 계층 구조를 따라, Ethernet, IP, TCP를 추출하였다. IF 문을 사용하여서, 해당 패킷이 TCP/IP 통신이 아니면 분석 안 하고 넘기게끔 작성해주고, 또한, POST 요청인지 확인하여, URL 파라미터에 저장시켜, 나중 응답과 연결시킨다. 

timestamp - request_time >= 3  부분을 통해, 응답이 3초 이상 걸리는지 즉, SLEEP(3)을 하는지 확인하여, 응답 패킷인지 확인한다. 그 후, 삽입된 SQL 코드에서 ASCII 값을 추출하여 flag_dict에 저장하여, flag 값을 완성시킨다.

 

1. pcap 파일에서 HTTP 패킷을 하나하나 읽기

2. POST 요청이 오면, 그 안에 있는 SQL 인젝션 코드를 파싱하여 저장

3. 그 후에 오는 HTTP 응답 패킷 확인

4. 응답이 3초 이상 걸리면, 서버가 SLEEP(3) 실행한 거임  →  조건에 부합

5. 그 요청에서 n 번째 글자, 어떤 ASCII 코드인지 찾아서 문자로 변환

6. 그것들을 다 저장하고, 모으면 플래그 출력

<br>

</br>

<img width="1280" height="1509" alt="image" src="https://github.com/user-attachments/assets/5a8fae28-4bcd-4baf-989c-8ae046d3128d" />

해당 파이썬 파일을 실행시키면, 다음과 같이 플래그 값을 얻을 수 있다!

<br>

</br>
<br>

</br>

FLAG ▷▶
```
GoN{T1mE_B4s3d_5QL_Inj3c7i0n_wI7h_Pc4p}
```
 
