<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h1> Dreamhack "BMP Recovery"  Write-UP</h1>
</body>
<br>
<br>
</html>

2025.07.27 풀이

<img width="580" height="1026" alt="image" src="https://github.com/user-attachments/assets/d321993e-88fb-4d1b-ae0d-72754584d374" />

다음 문제를 풀어보도록 하겠다.

<img width="1168" height="456" alt="image" src="https://github.com/user-attachments/assets/bdd94c25-51b7-413f-88ab-c33d2c3e7ea6" />

문제 파일에 있던 해당 코드는 정상적인 BMP 파일을 일부로 손상시키는 코드처럼 보인다.

data[0x00 ~ 0x1B] 구간을 전부 0으로 덮어씌우고, 앞에서부터 28바이트를 0x00으로 덮음으로써 BMP 헤더와 앞부분을 손상시키는 것처럼 보인다. 

<br>

</br>

<img width="1264" height="370" alt="image" src="https://github.com/user-attachments/assets/dc8fbd97-dcba-42c5-9f55-dc0df3cc49e8" />

flag.bmp.broken 파일을 HxD에서 열어보니, BMP 파일의 헤더와 앞부분이 손상되어 있다. 
각 필드에 들어가야 할 값이 들어있지 않아서 제대로 파일이 열리지 않는 것으로 보인다.
그래서 각 필드에 들어갈 BMP 표준 헤더 크기, info_header_size, bitplane_value, bytes_per_pixel 값을 심고, 파일 전체 크기까지 필드에 넣으면 완전히 복구될 것으로 보인다.

<br>

</br>

```
with open('flag.bmp.broken', 'rb') as f:
    original_data = bytearray(f.read())  

total_bytes = 14309622
header_size = 54
info_header_size = 40
bitplane_value = 1
bytes_per_pixel = 3

image_data_size = total_bytes - header_size
num_pixels = image_data_size // bytes_per_pixel

bmp_header_common = {
    0x00: b'BM',
    0x02: total_bytes.to_bytes(4, 'little'),
    0x0A: header_size.to_bytes(4, 'little'),
    0x0E: info_header_size.to_bytes(4, 'little'),
    0x1A: bitplane_value.to_bytes(2, 'little'),
    0x22: image_data_size.to_bytes(4, 'little'),
}

for width in range(1, int(num_pixels**0.5) + 1):
    if num_pixels % width != 0:
        continue

    height = num_pixels // width

    for (w, h) in [(width, height), (height, width)]:
        data = original_data.copy()

        # 공통 헤더 덮기
        for offset, value in bmp_header_common.items():
            data[offset:offset+len(value)] = value

        # 가로(width), 세로(height)
        data[0x12:0x16] = w.to_bytes(4, 'little')
        data[0x16:0x1A] = h.to_bytes(4, 'little')

        with open(f'flag_{w}x{h}.bmp', 'wb') as f:
            f.write(data)
```

다음과 같은 스크립트를 작성해보았다. 

BMP 복구에 필요한 값들을 각 필드에 넣고, 파일 전체 크기를 모르기 때문에, 반복루프를 사용해서, 여러 크기의 bmp를 만든 다음에, 그 중에서 제대로 복원된 BMP를 찾으려고 한다.

<br>

</br>

<img width="1536" height="1204" alt="image" src="https://github.com/user-attachments/assets/2f11e307-fb4b-4bf6-9276-4a2803121f5e" />

만들어진 여러 BMP 중 플래그 값이 나온 복구된 BMP를 찾을 수 있었다.

<br>

</br>

```
DH{c08ad9e275928481fe5aabac2a34b6573bf8dc7f8fb15d8b7120e069160a2c2f}
```
