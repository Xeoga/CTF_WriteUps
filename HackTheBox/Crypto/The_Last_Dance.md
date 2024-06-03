## Task
```
To be accepted into the upper class of the Berford Empire, you had to attend the annual Cha-Cha Ball at the High Court. Little did you know that among the many aristocrats invited, you would find a burned enemy spy. Your goal quickly became to capture him, which you succeeded in doing after putting something in his drink. Many hours passed in your agency's interrogation room, and you eventually learned important information about the enemy agency's secret communications. Can you use what you learned to decrypt the rest of the messages?
```
Dupa ce am dezarhivat arhiva avem 2 fisiere
- `out.txt` - care este flagul criptat
- `source.py` - care este algoritmul de criptare
```python
from Crypto.Cipher import ChaCha20
from secret import FLAG
import os


def encryptMessage(message, key, nonce):
    cipher = ChaCha20.new(key=key, nonce=iv)
    ciphertext = cipher.encrypt(message)
    return ciphertext


def writeData(data):
    with open("out.txt", "w") as f:
        f.write(data)


if __name__ == "__main__":
    message = b"Our counter agencies have intercepted your messages and a lot "
    message += b"of your agent's identities have been exposed. In a matter of "
    message += b"days all of them will be captured"

    key, iv = os.urandom(32), os.urandom(12)

    encrypted_message = encryptMessage(message, key, iv)
    encrypted_flag = encryptMessage(FLAG, key, iv)

    data = iv.hex() + "\n" + encrypted_message.hex() + "\n" + encrypted_flag.hex()
    writeData(data)

```
Ca sa primim flagul nostru decriptat folosim algoritmul urmator:
```python
#!/usr/bin/python3

e_message = bytes.fromhex("7aa34395a258f5893e3db1822139b8c1f04cfab9d757b9b9cca57e1df33d093f07c7f06e06bb6293676f9060a838ea138b6bc9f20b08afeb73120506e2ce7b9b9dcd9e4a421584cfaba2481132dfbdf4216e98e3facec9ba199ca3a97641e9ca9782868d0222a1d7c0d3119b867edaf2e72e2a6f7d344df39a14edc39cb6f960944ddac2aaef324827c36cba67dcb76b22119b43881a3f1262752990")
e_flag = bytes.fromhex("7d8273ceb459e4d4386df4e32e1aecc1aa7aaafda50cb982f6c62623cf6b29693d86b15457aa76ac7e2eef6cf814ae3a8d39c7")
message = b"Our counter agencies have intercepted your messages and a lot of your agent's identities have been exposed. In a matter of days all of them will be captured"

xored = b''
for i in range(len(e_flag)):
    xored += bytes([e_flag[i] ^ e_message[i]])

print(xored)
print(xored.hex())

pt = b""
for i in range(len(xored)):
    pt += bytes([xored[i] ^ message[i]])

print(pt)
```
A XOR B XOR B=A

Aplicând această proprietate:

1. Dacă avem un text clar message și un mesaj criptat e_messagee message, putem calcula un XOR intermediar xored
2. XOR-ul dintre mesajul criptat și steagul criptat ne oferă informații intermediare.
3. Aplicând XOR din nou cu textul clar cunoscut, obținem textul clar dorit.
Deci primim flagul nostru:
`b'HTB{und3r57AnD1n9_57R3aM_C1PH3R5_15_51mPl3_a5_7Ha7}'`
![[xor_the_last_danc.png]]
Flagul final:
`HTB{und3r57AnD1n9_57R3aM_C1PH3R5_15_51mPl3_a5_7Ha7}`