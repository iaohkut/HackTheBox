# xorxorxor
#### Tải xuống file nén, sau đó giải nén chúng, chúng ta nhận được 2 file nhỏ bên trong chúng
* challenge.py
```python
#!/usr/bin/python3
import os
flag = open('flag.txt', 'r').read().strip().encode()
class XOR:
    def __init__(self):
        self.key = os.urandom(4)
    def encrypt(self, data: bytes) -> bytes:
        xored = b''
        for i in range(len(data)):
            xored += bytes([data[i] ^ self.key[i % len(self.key)]])
        return xored
    def decrypt(self, data: bytes) -> bytes:
        return self.encrypt(data)
def main():
    global flag
    crypto = XOR()
    print ('Flag:', crypto.encrypt(flag).hex())
if __name__ == '__main__':
    main()

```
* output.txt
> Flag: 134af6e1297bc4a96f6a87fe046684e8047084ee046d84c5282dd7ef292dc9

Trước tiên, chúng ta bắt đầu với source code python
Có 1 class tên là XOR, và nó chứa 3 hàm, hàm đầu tiền là init, dùng để khởi tạo một chuỗi hex ngẫu nhiên có 4 bytes
```python 
    def __init__(self):
        self.key = os.urandom(4)
```

Sau đó, hàm encrypt dùng để xor từng byte một với nhau giữa data value được truyền vào và key được tạo ở hàm init
```python 
    def encrypt(self, data: bytes) -> bytes:
        xored = b''
        for i in range(len(data)):
            xored += bytes([data[i] ^ self.key[i % len(self.key)]])
        return xored
```
Cuối cùng là hàm decrypt trả về giá trị data đã được mã hóa trước đó.

Phía trong hàm main, chúng in ra một chuỗi hex đã được mã hóa từ flag thông qua các thuật toán ở XOR class. Kết quả chúng ta nhận được nằm ở file output.txt

### Solution: 

Vì ở đây nó là một thử thách CTF, vậy nên chúng ta có thể đoán được flag sẽ có dạng HTB{}. Vậy chúng ta đã có 4 bytes đầu của flag và dùng nó để đối chiếu với dữ liệu mình nhận được. Vì ở đây khóa được tạo là ngẫu nhiên dài 4bytes nên chúng ta có thể dễ dàng xor ngược lại và tìm được khóa.

```python
def xor(var, key):
    return (var ^ key)

key = []
key.append(xor(ord("H"), int("13",16)))
key.append(xor(ord("T"), int("4a",16)))
key.append(xor(ord("B"), int("f6",16)))
key.append(xor(ord("{"), int("e1",16)))
```

> Key: [91, 30, 180, 154]

Bây giờ, chúng ta đã có khóa và flag ở dạng ciphertext, việc tiếp theo là decode flag này dựa theo khóa và tìm ra flag dạng plaintext. Chúng ta chỉ cần xor từng byte với nhau của khóa và flag để có được kết quả mong muốn.

#### Source code:
```python
flag = b'134af6e1297bc4a96f6a87fe046684e8047084ee046d84c5282dd7ef292dc9'

def xor(var, key):
    return (var ^ key)

key = []
key.append(xor(ord("H"), int("13",16)))
key.append(xor(ord("T"), int("4a",16)))
key.append(xor(ord("B"), int("f6",16)))
key.append(xor(ord("{"), int("e1",16)))

hexvalue = []
string = ""
for i in range(0, len(flag), 2):
    number = xor(int(flag[i:i+2],16), key[int((i/2)%4)])
    string += chr(number)

print(string)
```
### Flag: HTB{rep34t3d_x0r_n0t_s0_s3cur3}  
