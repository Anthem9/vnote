# sm
```python
import base64
import hashlib
import binascii

ef = b"5eFo3ANg2fu9LRrFktWCJmVvx6RgBFzd0R8GXQ8JD78="
r=6753785483255906709117615805253027649453460653974415214642466102672301763943358839905575042938258141827000621474498066533397472809407687579125519939754658

def num_to_string(x):
    hex_x = hex(x)[2:]
    if len(hex_x) % 2 == 1:
        hex_x = '0' + hex_x
    res = bytearray()
    for i in range(len(hex_x) >> 1):
        res.append(int(hex_x[i * 2: i * 2 + 2], 16))
    return bytes(res)

ps = []
f = open("ps", "r")
while True:
    line = f.readline()
    if not line:
        break
    ps.append(int(line))

mat = []
b = [0] * 512
for i in range(512):
    mat.append([0] * 512)

for i in range(512):
    for j in range(512):
        mat[i][j] = (ps[j] >> (511 - i)) & 1
    b[i] = (r >> (511 - i)) & 1

for i in range(512):
    for j in range(i, 512):
        if mat[j][i] == 1:
            mat[i], mat[j] = mat[j], mat[i]
            b[i], b[j] = b[j], b[i]
            break
    for j in range(512):
        if j != i and mat[j][i] == 1:
            for k in range(i, 512):
                mat[j][k] ^= mat[i][k]
            b[j] ^= b[i]

ch = 0
for i in range(512):
    tmp = 0
    for j in range(512):
        tmp = (tmp << 1) | mat[i][j]
    if tmp != (1 << (511 - i)):
        print("Wrong at", i)
        exit(0)
    ch = (ch << 1) | b[i]

bch = bin(ch)[2:]
bch = "0" * (512 - len(bch)) + bch
rr = 0
for i in range(512):
    if bch[i] == '1':
        rr = rr ^ ps[i]
def num_to_string(x):
    hex_x = hex(x)[2:]
    if len(hex_x) % 2 == 1:
        hex_x = '0' + hex_x
    res = bytearray()
    for i in range(len(hex_x) >> 1):
        res.append(int(hex_x[i * 2: i * 2 + 2], 16))
    return bytes(res)
key = hashlib.md5(num_to_string(ch)).hexdigest()
cipher = binascii.b2a_hex(base64.b64decode(ef))

print(key)
print()
print(cipher)
```
将此异或方程组，直接化成 512 * 512 的矩阵，然后高斯消元。

将得到的key和密文使用在线网站解密。