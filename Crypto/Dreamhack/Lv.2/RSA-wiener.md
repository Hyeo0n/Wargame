<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "RSA-wiener"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.10.28 풀이

<img width="582" height="764" alt="image" src="https://github.com/user-attachments/assets/b3b080d2-bc23-4a91-8da9-795f07835eb6" />

다음과 같은 문제를 풀어보려고 한다.
<br>

<img width="1178" height="442" alt="image" src="https://github.com/user-attachments/assets/6ee5a7e0-70e8-48fc-a8a8-6bedf5129bc2" />

<img width="1464" height="254" alt="image" src="https://github.com/user-attachments/assets/e0655a11-e96d-4ad7-baf6-132e5a668b9a" />

주어진 문제 파일 `public_key.pem.txt`, `flag.txt`은 다음과 같다. 
<br>

<img width="1154" height="1282" alt="image" src="https://github.com/user-attachments/assets/3df6b25b-953c-4c01-b266-8b428f7117da" />

```
Algo RSA
Format X.509
 ASN1 Dump
RSA Public Key [b5:ea:05:7f:46:9f:66:39:5f:09:6b:0e:d0:75:65:9a:f3:36:ab:20]
            modulus: 1523ae8fa484c5aed5b4752f6db9d8e7b68654cf5baf3a79a7f22ae0ee0270b69ae12b23ea5736018ea2007220722583c2b615ae798ac845da15566f8efb21f4db27b83ab946921edd7f46027bc7e815913b1a4fc26c8fb542dcc8215f931e64476b234125536d15e8b2d7bac4dce6eb7e76e269e7849d858573cc2f383fbd299
    public exponent: 10a9db9487ee5dfc2d0a295dfa8944b315e636ee5cfd11a2e003969a68ccaaf1a85d834a5b952f249c088639c0914e4988c5930239dd5d918267bc56819b09fe2ce20d556c6b75b3144878f78dd342f6b72c6f61c0e5d1603d8162c49a8d9ed15b3ff22ea291026044e528b6753c54866c07812712283b12cea28185c60541dd9
```

Base64로 인코딩된 문제 파일 `public_key.pem.txt`을 디코딩해주는 특정 사이트를 이용하여 디코딩해주었다.
<br>

<img width="830" height="816" alt="image" src="https://github.com/user-attachments/assets/a7ca91b8-f7d5-4177-ac95-9569d90ad962" />

위의 pem 파일을 openssl 명령어를 통해 10진수로 변환해주었다. 
<br>

<img width="1462" height="360" alt="image" src="https://github.com/user-attachments/assets/dd494a81-c0a2-4c6b-847f-105f7064554c" />

<img width="1440" height="302" alt="image" src="https://github.com/user-attachments/assets/3eb36cd3-4c7b-446a-8179-3ab95c5eefb7" />

그 후, 이를 다시 파이선 코드를 통해, 16진수로 변환해주었다. flag.txt 파일의 내용도 같이 변환해주었다.

<br>

wiener 공격은 n, e를 알고 있는 상태에서 RSA의 개인키 d를 구하기 위해, d의 근사치를 계산하는 공격을 말한다.
현재 지금 modulus(n), exponent(e)를 알고 있으므로, 이를 통해 d를 구한 다음에 flag값을 디코딩하면 플래그 값을 얻을 수 있을 것으로 보인다.

<br>

<img width="1378" height="618" alt="image" src="https://github.com/user-attachments/assets/9fecd841-9adf-4ccc-ba1d-97b8ec55ef2c" />

이 과정은 `RSHack`라는 툴을 이용하여 진행해보도록 하겠다.
<br>

<img width="2854" height="1488" alt="image" src="https://github.com/user-attachments/assets/eba2e51f-fdf6-4d5b-87c0-45185c6747f0" />

해당 툴에서 1 과정을 선택 후, 16진수로 변환한 n과 e를 넣어주면, wiener 공격이 진행되어, priviate exponent (d)를 얻을 수 있다.
<br>

<img width="2866" height="1506" alt="image" src="https://github.com/user-attachments/assets/713619fa-6c64-4470-94ff-a36ab9a3790b" />

위에서 구한 d와 e, 그리고 앞에서 16진수로 변환한 flag.txt 를 넣어주면, 플래그 값을 얻을 수 있다.
<br>

```
KCTF{We1l_kn0wn_rSa_ATTAcK}
```


