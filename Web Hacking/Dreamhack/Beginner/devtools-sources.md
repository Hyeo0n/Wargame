<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "devtools-sources"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.05.02 풀이

![image](https://github.com/user-attachments/assets/61af0b82-2899-4543-b86e-cb671ec06084)

https://chs777888.tistory.com/11

에서 개발자 도구에 대해서, 학습한 후, 

이를 실습으로 익히기 위해, Dreamhack 워게임에 있는 비기너 단계의 

devtools-sources를 풀어보았다.

문제를 다운받은 후, 압축을 풀어보니, html, css, js 파일 위주로 많이 있었다.
<br>

</br>

![image](https://github.com/user-attachments/assets/5a39ef80-bd07-4f1d-8a75-f3fce2dcefa3)

일단, html 파일들 위주로 창을 열어 F12키를 눌러, 개발자 도구를 실행했다. 
소스 창에서 Ctrl + F키를 눌러, DH를 검색하여, 해당 문자열이 해당 파일이 속해 있는지 찾아보았다.

<br>

</br>

## index.html
![image](https://github.com/user-attachments/assets/dd427b9c-0345-48a5-bcad-a72bcb317824)

## index.html 소스
![image](https://github.com/user-attachments/assets/1ce95e90-ac8e-455e-a7b8-1117a4c28775)

Ctrl+F 키를 눌러, index.html에서 dh 문자열을 검색해본 결과, 발견되지 않았다.

<br>

</br>            

## sample-page.html
![image](https://github.com/user-attachments/assets/527c9b89-dd04-48c4-adf4-56e44788f3cb)

## sample-page.html 소스
 ![image](https://github.com/user-attachments/assets/7c3e21cb-e0e1-44e7-9b87-b1d9bf5b1b01)

Ctrl+F 키를 눌러, sample-page.html에서 dh 문자열을 검색해본 결과, 발견되지 않았다.

<br>

</br> 

## about.html
![image](https://github.com/user-attachments/assets/a04cf1f0-c835-4700-933a-a7bcfaa5e36a)

## about.html 소스
![image](https://github.com/user-attachments/assets/4f15bfa8-874b-4aef-84cb-dc4d469cdc78)

Ctrl+F 키를 눌러, about.html에서 dh 문자열을 검색해본 결과, 발견되지 않았다.

 <br>

</br> 

## components.html
![image](https://github.com/user-attachments/assets/6295c434-f62c-46ca-a3e1-042dd709f7d7)

## components.html 소스
![image](https://github.com/user-attachments/assets/0ffdf14a-befb-4695-b682-88b93557f86d)

Ctrl+F 키를 눌러, components.html에서 dh 문자열을 검색해본 결과, 발견되지 않았다.

<br>

</br> 

## project.html
![image](https://github.com/user-attachments/assets/773fcb88-d506-41a3-8c5f-696386a56e43)

## project.html 소스
![image](https://github.com/user-attachments/assets/9ef869c6-9ea2-497c-8af5-6978b90a89f3)

Ctrl+F 키를 눌러, project.html에서 dh 문자열을 검색해본 결과, 발견되지 않았다.
HTML 파일에서는 다 발견되지 않아서, css 파일과 js 파일에서도 검색해보았다.

<br>

</br>

## css 파일
![image](https://github.com/user-attachments/assets/22e1cf8f-6a6a-45c9-acbe-a585f4832867)

Ctrl+F 키를 눌러, main.2da94fde.css에서 dh 문자열을 검색해본 결과, 발견되지 않았다.

 <br>

</br>

## js 파일
![image](https://github.com/user-attachments/assets/07efef61-b08d-459d-9976-e8b600ce569e)
 
Ctrl+F 키를 눌러, main.4c6e144e.js에서 dh 문자열을 검색해본 결과, 발견되지 않았다.
웬만한 파일에 다 DH 문자열을 검색해본 결과, 별다르게 발견된 게 없었다.
<br>

</br>
 <br>

</br>

![image](https://github.com/user-attachments/assets/9bfff9bd-18c8-4492-9e3a-d9a1dcec828a)

구글 개발자 도구에서 전체 파일에서 전역 검색하는 키인 Ctrl + Shift + F 키를 눌러보니,
styles 파일에 숨어있는 main.css 파일에서 DH 문자열을 발견해냈다.
flag로 보이는 부분이 주석 처리되어 있는 것을 확인할 수 있었다. 이를 복붙하여, 

 <br>

</br>

```
DH{2ed07940b6fd9b0731ef698a5f0c065be9398f7fa00f03ed9da586c3ed1d54d5}
```
 flag에 입력해보니, 

<br>

</br>

![image](https://github.com/user-attachments/assets/3a739211-f412-49b0-94dc-2317485ff5f5)

문제 풀이에 성공했다.
