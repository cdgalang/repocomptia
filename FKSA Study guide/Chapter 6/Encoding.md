# Encoding

## Base 64 encoding
- computer code using 64 characters to encode any binary string with text
- requires binary input/data
- Base64 encryption begins by splitting the message into groups of 6 bits, supplemented with 0 if necessary
- each 6 bit group is then converted into a character from a predefined 64-character alphabet
- standardize to have groups of 4 characters

- How to decypt?
- conver each character to its 6-bit binary equivalent, then recomine these groups to find the original binary data.
- how to recognize a base64 ciphertext
- composed of a number of characters multiple of 4
- the resence of equal (=) at the end of the message is a big clue

- Why use Base64?
- Base64 encoded message will only contain printable ASCII charcters 

Practice

Turn "Cat" into Base64

1) Turn letter into ASCII decimal
C = 67 
a = 97
t = 116

2) Turn ASCII decimal to 8 bit binary
67 = 01000011
97 = 01100001
116 = 01110100

3) this is what binary looks like
01000011 01100001 01110100

4) turn into 6 bit
010000 110110 000101 110100

5) turn into decimal
16, 54, 5, 52

6) turn into base64
Q2F0

Cat = Q2F0

## Caesar Cipher

- oldest and simplest encryption methods
- substitution cipher
- think of it as a wheel


## ROT-13 Cipher

- Rotation 13
- combines the french/latin alphabet of 26 letters and an offset of 13, fixed to 13