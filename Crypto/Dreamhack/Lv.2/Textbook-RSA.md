<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "Textbook-RSA"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.10.28 풀이

<img width="606" height="612" alt="image" src="https://github.com/user-attachments/assets/b55bb188-d840-41c1-a034-33817d10a18a" />

해당 문제를 풀이해보려고 한다.
<br>

```
#!/usr/bin/python3
from Crypto.Util.number import getStrongPrime, bytes_to_long, inverse


class RSA(object):
    def __init__(self):
        self.p = getStrongPrime(512)
        self.q = getStrongPrime(512)
        self.N = self.p * self.q
        self.e = 0x10001
        self.d = inverse(self.e, self.N - self.p - self.q + 1)

    def encrypt(self, pt):
        return pow(pt, self.e, self.N)

    def decrypt(self, ct):
        return pow(ct, self.d, self.N)


rsa = RSA()
FLAG = bytes_to_long(open("flag", "rb").read())
FLAG_enc = rsa.encrypt(FLAG)

print("Welcome to dream's RSA server")

while True:
    print("[1] Encrypt")
    print("[2] Decrypt")
    print("[3] Get info")

    choice = input()

    if choice == "1":
        print("Input plaintext (hex): ", end="")
        pt = bytes_to_long(bytes.fromhex(input()))
        print(rsa.encrypt(pt))

    elif choice == "2":
        print("Input ciphertext (hex): ", end="")
        ct = bytes_to_long(bytes.fromhex(input()))
        if ct == FLAG_enc or ct > rsa.N:
            print("Do not cheat !")
        else:
            print(rsa.decrypt(ct))

    elif choice == "3":
        print(f"N: {rsa.N}")
        print(f"e: {rsa.e}")
        print(f"FLAG: {FLAG_enc}")

    else:
        print("Nope")

```

문제 파일 코드를 살펴보겠다.

코드를 살펴보니, 공개 키 값으로 65537와 n, e, d를 사용하여 RSA 키를 생성하는 것으로 보인다.
flag 파일을 읽어들어서 rsa.encrypt를 통해 flag를 암호화하는 것으로 보인다.
밑에 1,2,3 옵션을 살펴보니, 1번은 encrypt로 16진수 값을 입력받고 이를 rsa의 encrypt 부분을 통해 암호화하는 것을 알 수 있다.

그 후, 2번에서는 decrypt를 진행하는데, 16진수 값을 입력받고 이를 복호화하는 것으로 알 수 있다. 
3번은 현재 N, e, 암호화된 flag 값을 보여주는 것으로 보인다. 
<br>

<img width="1758" height="1398" alt="image" src="https://github.com/user-attachments/assets/fa016111-31b6-4230-8e09-f7aef1ca298c" />

드림핵 서버를 열어준 후, pwntool를 이용하여, 서버에 접근하여 3번 옵션으로 들어가 필요한 값들을 얻고 이를 복호화해주는 스크립트를 작성하였다.
<br>

<img width="1500" height="658" alt="image" src="https://github.com/user-attachments/assets/391a4f96-5f47-4322-baea-7c831cc4799c" />

해당 스크립트를 실행해보면 다음과 같이 플래그 값을 얻을 수 있다.
<br>

```
DH{6623c33be90cc27728d4ec7287785992}
```
