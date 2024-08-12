<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "file-download-1"  Write-UP</h1>
</body>
<br>
<br>
</html>

2024.08.13 풀이

![image](https://github.com/user-attachments/assets/723f9687-f2f6-4f8e-b8e1-a40e4bf365fc)

https://chs777888.tistory.com/73
에서 File Vulnerability에 대해서, 학습한 후,

이를 실습으로 익히기 위해, Dreamhack 워게임에 있는 1 단계의

file-download-1를 풀어보려고 한다.

<br>
</br>

일단 해당 서버에 접속해보니, 메모를 공유하라는 문구가 뜬다.


![image](https://github.com/user-attachments/assets/cc1d4159-a1ad-450b-be0b-49f2a8c62f76)

<br>

</br>

## Upload My Memo 페이지
![image](https://github.com/user-attachments/assets/828d3765-9049-4349-b997-4f18da8a0447)

파일명을 짓고, 파일 내용을 작성하고, 업로드하는 것 같다.. 이제 코드를 한 번 살펴보려고 한다

<br>

</br>  

## app.py

![image](https://github.com/user-attachments/assets/ba9d27c3-a33a-4fb3-a4e4-64d178c9dc38)
![image](https://github.com/user-attachments/assets/0a2d78c1-2387-4dcb-b215-753b0675ddc7)

<br>

</br>  

## /upload
```
@APP.route('/upload', methods=['GET', 'POST'])
def upload_memo():
    if request.method == 'POST':
        filename = request.form.get('filename')
        content = request.form.get('content').encode('utf-8')

        if filename.find('..') != -1:
            return render_template('upload_result.html', data='bad characters,,')

        with open(f'{UPLOAD_DIR}/{filename}', 'wb') as f:
            f.write(content)

        return redirect('/')

    return render_template('upload.html')

```

해당 부븐을 살펴보면, 파일을 업로드하는 기능들을 표시하고 있다. POST 요청 시 폼 데이터를 받아 파일을 저장하는데, 이때 파일 이름에 ``..`` 문자열이 포함되어 있으면, 업로드를 거부하는 것을 볼 수 있다. 이는 디렉토리 트래버셜 공격을 방지하기 위한 것으로 추측된다. 그리고 파일은 ``바이너리 (wb)`` 모드로 uploads 디렉토리에 저장되는 것을 알 수 있다. 

<br>

</br>  

## /read

```
@APP.route('/read')
def read_memo():
    error = False
    data = b''

    filename = request.args.get('name', '')

    try:
        with open(f'{UPLOAD_DIR}/{filename}', 'rb') as f:
            data = f.read()
    except (IsADirectoryError, FileNotFoundError):
        error = True

    return render_template('read.html',
                           filename=filename,
                           content=data.decode('utf-8'),
                           error=error)

```
해당 부분을 살펴보면, 업로드된 파일을 읽어주는 부분인 것 같다. ``{UPLOAD_DIR}/{filename}``을 보면, 업로드된 파일들의 디렉토리가 filename 바로 위 디렉토리인 것을 확인할 수 있다.

<br>

</br>  
<br>

</br>  
## flag
![image](https://github.com/user-attachments/assets/ec52e6ad-31b1-404a-a02f-ba242c7c816b)

 맨 처음에 flag.py를 다운로드 받아야 flag 값을 알 수 있다고 했기 때문에, 일단 위에처럼 임의로 flag.py 파일을 업로드해보려고 한다. 
 <br>

</br>  

![image](https://github.com/user-attachments/assets/2f7dae28-d85d-4847-8458-bc9cf3378af2)

해보니까, flag.py 파일이 생성되었다.
 <br>

</br>  

![image](https://github.com/user-attachments/assets/9ed92709-85da-4055-8e94-901d33ca7992)

들어가보니, 위에 작성한 내용 그대로 출력되었다. 확인해보니, 내가 작성한 파일명 그대로 위에 파라미터 ``read?name=flag.py``에 전달된 것을 볼 수 있었다. 
 <br>
</br>  
아까 /read 페이지에서 발견했던 것처럼, 업로드된 파일들은 전부 filename 바로 위 디렉토리에 존재하기 떄문에, 파일명을 수정해서 다시 업로드해보려고 한다. 

 <br>
 
</br>  

![image](https://github.com/user-attachments/assets/370bd7dc-e904-4e85-9d6e-deb326a24e56)

![image](https://github.com/user-attachments/assets/93a6da45-187b-4dc1-86e2-c5d89b4387b4)

상위 디렉토리를 나타내기 위해 ``../flag.py``라고 파일명을 작성하였는데, /upload 페이지에서 봤던 것처럼 .. 필터링으로 인해 업로드되지 않는 것을 확인할 수 있다.
 <br>
 
</br> 
그래서, 필터링을 피하기 위해,  .. 를 인코딩해서 넣어보려고 한다. .. 를 인코딩하면,

``%2e%2e``이 되므로, ``%2e%2e/flag.py`` 로 업로드해보려고 했는데, 

![image](https://github.com/user-attachments/assets/5fd4214b-782c-46b0-bcce-8fbc6b2183fa)

페이지 자체가 나가졌다.
 <br>
 
</br>
그래서, / 까지 인코딩한 

``%2e%2e%2fflag.py`` 으로 다시 작성해보았다.

 <br>
 
</br>

![image](https://github.com/user-attachments/assets/606a5c71-b5e3-4ef2-a54e-ec6cecee159e)

![image](https://github.com/user-attachments/assets/68e3349e-3acd-4d92-9d04-99d6e06b28f9)

 <br>
 
</br>
 <br>
 
</br>

![image](https://github.com/user-attachments/assets/bf6b327b-5dc7-45b1-8d9e-cb869d577598)
flag 값을 얻을 수 있었다!
 <br>
</br>

```
DH{uploading_webshell_in_python_program_is_my_dream}
```
 <br>
</br>

![image](https://github.com/user-attachments/assets/62dbc679-4752-4bfd-a24c-a2781c0acdd2)

이렇게 문제를 해결할 수 있었다.
