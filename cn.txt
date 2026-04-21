Q1
  copy /B image.jpg + label.zip new.jpg 
	BMP-42 4D
	Gif-47 49 46 38 37 61
	png-89 50 4E 47 0D 0A 1A 0A
	Tif- 49 49 2A 00
	jpg FF d8 FF E0
Q4
text encrypt & decrypt
 touch file1.txt
 gedit file1.txt

cbc-:
openssl enc -aes-128-cbc -e -in file1.txt -out ciphers.txt -k 00112233445566778899aabbccddeeff -iv 0102030405060708090a0b0c0d0e0f
cat ciphers.txt
openssl enc -aes-128-cbc -d -in ciphers.txt -out outputs.txt -k 00112233445566778899aabbccddeeff -iv 0102030405060708090a0b0c0d0e0f

cat outputs.txt

ecb:-
openssl enc -aes-128-ecb -e -in file1.txt -out cipher5.txt -k 00112233445566778899aabbccddeeff
openssl enc -aes-128-ecb -d -in cipher5.txt -out output1.txt -k 00112233445566778899aabbccddeeff
cat output1.txt
xxd ciphers.txt
xxd ciphers5.txt

Q5
image encryption

ecb-
openssl enc -aes-128-ecb -e -in pic_original.bmp -out ecb1.bmp -k 00112233445566778899aabbccddeeff
head -c 54 pic_original.bmp > header
tail -c +55 ecb1.bmp > body-ecbs
cat header body-ecbs > newecb.bmp

cbc-
openssl enc -aes-128-cbc -e -in pic_original.bmp -out cbc1.bmp -k 00112233445566778899aabbccddeeff -iv 0102030405060708090a0b0c0d0e0f
tail -c +55 cbc1.bmp > body-cbcs
cat header body-cbcs > newcbc.bmp

eog newecb.bmp
eog newcbc.bmp

q6
padding

echo -n 12345 > t11.txt
echo -n 1234567890 > t22.txt
echo -n 1234567890abcdef > t33.txt
openssl enc -aes-128-cbc -e -in t11.txt -out outpt11.bin -K 00112233445566778899aabbccddeeff -iv 010203040506070890a0b0c0d0e0f
openssl enc -aes-128-cbc -d -in outpt11.bin -out outpt12.txt -K 00112233445566778899aabbccddeeff -iv 010203040506070890a0b0c0d0e0f
openssl enc -aes-128-cbc -d -in outpt11.bin -out outpt12_nopad.txt -nopad -K 00112233445566778899aabbccddeeff -iv 010203040506070890a0b0c0d0e0f
xxd outpt12.txt
xxd outpt12_nopad.txt

openssl enc -aes-128-cbc -e -in t22.txt -out outpt22.bin -K 00112233445566778899aabbccddeeff -iv 010203040506070890a0b0c0d0e0f
openssl enc -aes-128-cbc -d -in outpt22.bin -out outpt21.txt -K 00112233445566778899aabbccddeeff -iv 010203040506070890a0b0c0d0e0f
openssl enc -aes-128-cbc -d -in outpt22.bin -out outpt22_nopad.txt -nopad -K 00112233445566778899aabbccddeeff -iv 010203040506070890a0b0c0d0e0f
xxd outpt21.txt
xxd outpt22_nopad.txt

q7
error

gedit text1.txt
openssl enc -aes-128-ecb -e -in text1.txt -out outpt1.txt -K 00112233445566778899aabbccddeeff
ghex outpt1.txt
openssl enc -aes-128-ecb -d -in outpt1.txt -out outpt2.txt -K 00112233445566778899aabbccddeeff

cat text1.txt
cat outpt2.txt


openssl enc -aes-128-cbc -e -in text1.txt -out outpt3.txt -K 00112233445566778899aabbccddeeff -iv 0102030405060708090a0b0c0d0e0f11
ghex outpt3.txt
openssl enc -aes-128-cbc -d -in outpt3.txt -out outpt4.txt -K 00112233445566778899aabbccddeeff -iv 0102030405060708090a0b0c0d0e0f11
cat text1.txt
cat outpt4.txt

openssl enc -aes-128-cbc -e -in text1.txt -out outpt3.txt -k 00112233445566778899aabbccddeeff -iv 0102030405060708090a0b0c0d0e0f11
xxd outpt3.txt
openssl enc -aes-128-cbc -e -in text1.txt -out outpt5.txt -k 00112233445566778899aabbccddeeff -iv 102030405060708090a0b0c0d0e0f110
xxd outpt5.txt


q8-
rsa- 

openssl rand -hex 16
openssl rand -hex 32
openssl genrsa 1024 > key.pri
cat key.pri
openssl rsa -in key.pri -out key.pub -pubout
cat key.pub

gedit earth.txt
openssl rsautl -encrypt -inkey key.pub -pubin -in earth.txt -out earth.enc
xxd earth.enc
openssl rsautl -decrypt -inkey key.pri -in earth.enc -out earth.dec
cat earth.dec

openssl rand -hex -out encryption.key 32
openssl rsautl -encrypt -inkey key.pub -pubin -in encryption.key -out encryption.key.enc
xxd encryption.key.enc
openssl rsautl -decrypt -inkey key.pri -in encryption.key.enc -out encryption.key.dec
cat encryption.key.dec


q9-tcpdump

sudo tcpdump -D
sudo tcpdump -i enp0s3
ping google.com
ping 142.250.77.142
sudo tcpdump host 142.250.77.142
sudo tcpdump -i enp0s3 icmp
sudo tcpdump port 80
http://example.com
sudo tcpdump -i enp0s3 -w file.pcap
sudo tcpdump -r file.pcap
sudo tcpdump -A -r file.pcap
sudo tcpdump -xx -r file.pcap
sudo tcpdump tcp
sudo tcpdump -i enp0s3 tcp
sudo tcpdump -n -i enp0s3 'tcp[tcpflags] & tcp-syn != 0'
sudo tcpdump -n -i enp0s3 'tcp[tcpflags] & tcp-syn != 0' -w cap.pcap
sudo tcpdump -n -i enp0s3 'tcp[tcpflags] & tcp-ack != 0' -w file1.pcap
sudo tcpdump -n -xx -r file1.pcap
sudo tcpdump -n -i enp0s3 'tcp[tcpflags] & (tcp-syn|tcp-ack) != 0' -w file2.pcap
sudo tcpdump -n -xx -r file2.pcap
sudo tcpdump -i enp0s3 -nn -p -e host example.com and port 443 and 'tcp[tcpflags] & (tcp-syn|tcp-ack) != 0'


Q 3

import numpy as np
from PIL import Image

def Encode(src, message, dest):
    img = Image.open(src, 'r')
    width, height = img.size
    array = np.array(list(img.getdata()))

    if img.mode == 'RGB':
        n = 3
    elif img.mode == 'RGBA':
        n = 4

    total_pixels = array.size // n
    message += "$t3g0"
    b_message = ''.join([format(ord(i), "08b") for i in message])

    req_pixels = len(b_message)

    if req_pixels > total_pixels:
        print("ERROR: Need larger file size")
    else:
        index = 0
        for p in range(total_pixels):
            for q in range(0, 3):
                if index < req_pixels:
                    array[p][q] = int(bin(array[p][q])[2:9] + b_message[index], 2)
                    index += 1

        array = array.reshape(height, width, n)
        enc_img = Image.fromarray(array.astype('uint8'), img.mode)
        enc_img.save(dest)
        print("Image Encoded Successfully")

def Decode(src):
    img = Image.open(src, 'r')
    array = np.array(list(img.getdata()))

    if img.mode == 'RGB':
        n = 3
    elif img.mode == 'RGBA':
        n = 4

    total_pixels = array.size // n
    hidden_bits = ""

    for p in range(total_pixels):
        for q in range(0, 3):
            hidden_bits += bin(array[p][q])[2:][-1]

    hidden_bits = [hidden_bits[i:i+8] for i in range(0, len(hidden_bits), 8)]

    message = ""
    for i in range(len(hidden_bits)):
        if message[-5:] == "$t3g0":
            break
        else:
            message += chr(int(hidden_bits[i], 2))

    if "$t3g0" in message:
        print("Hidden Message:", message[:-5])
    else:
        print("No hidden message found")

def Stego():
    print("----- Welcome to Stego -----")
    print("1: Encode")
    print("2: Decode")

    func = input()

    if func == '1':
        print("Enter Source Image Path")
        src = input()
        print("Enter Message to Hide")
        message = input()
        print("Enter Destination Image Path")
        dest = input()
        print("Encoding...")
        Encode(src, message, dest)

    elif func == '2':
        print("Enter Source Image Path")
        src = input()
        print("Decoding...")
        Decode(src)

    else:
        print("ERROR: Invalid option chosen")

Stego()




[or]


import numpy as np
from PIL import Image

# ---------- ENCODE ----------
def encode(src, message, dest):
    img = Image.open(src)
    data = np.array(img)

    message += "stego"   # end marker
    binary = ''.join(format(ord(i), '08b') for i in message)

    index = 0
    for i in range(data.shape[0]):
        for j in range(data.shape[1]):
            for k in range(3):   # RGB
                if index < len(binary):
                    data[i][j][k] = int(bin(data[i][j][k])[2:9] + binary[index], 2)
                    index += 1

    Image.fromarray(data).save(dest)
    print("Image Encoded Successfully!")

# ---------- DECODE ----------
def decode(src):
    img = Image.open(src)
    data = np.array(img)

    bits = ""
    for i in range(data.shape[0]):
        for j in range(data.shape[1]):
            for k in range(3):
                bits += bin(data[i][j][k])[-1]

    chars = [bits[i:i+8] for i in range(0,len(bits),8)]

    message = ""
    for c in chars:
        message += chr(int(c,2))
        if message.endswith("stego"):
            print("Hidden message:", message[:-5])
            return

    print("No hidden message found")


# ---------- MENU ----------
print("1. Encode")
print("2. Decode")
choice = input()

if choice == '1':
    encode(input("Source image: "), input("Message: "), "output.png")
elif choice == '2':
    decode(input("Encoded image: "))
else:
    print("Invalid option")
