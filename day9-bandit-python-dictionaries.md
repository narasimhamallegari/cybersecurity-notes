# Day 9 — Base64 and ROT13, Python Dictionaries, Port Scanner Upgraded

Date: April 11, 2026
Time spent: 2 hours

---

## How the day went

Two things clicked today. First: base64 is not encryption — it is just encoding. That distinction matters significantly in security work. Second: Python dictionaries made the port scanner immediately more useful. Adding service name lookup to the scanner was the first time I modified a tool I built and made it meaningfully better.

---

## Security+ — Zero Trust and Cryptography Basics

### Zero Trust Architecture

Old model: build a wall around the network, trust everything inside. Failed when remote work dissolved the perimeter.

Zero Trust principle: never trust, always verify. Every access request must be authenticated and authorized, regardless of whether it comes from inside or outside the network.

Zero Trust components:
- Identity verification on every single access request
- Device health validation before granting access
- Least privilege — only what is needed for the specific task
- Micro-segmentation — dividing the network into small isolated zones
- Continuous monitoring

### Cryptography Basics

Symmetric encryption: The same key encrypts and decrypts. Fast. The problem is secure key distribution. Examples: AES, 3DES.

Asymmetric encryption: Public key encrypts, private key decrypts. Solves key sharing. Slower. Examples: RSA, ECC.

Hashing: One-way transformation. Cannot be reversed. Fixed-length output regardless of input size. Used for password storage, file integrity, and digital signatures. Examples: MD5 (broken), SHA-1 (deprecated), SHA-256 (current standard).

How HTTPS uses both: Asymmetric encryption is used during the TLS handshake to securely exchange a symmetric key. Then, symmetric encryption is used for data transfer because it is faster.

---

## Bandit Level 9 to 10: Base64 Decoding

Command used:
cat data.txt | base64 --decode

What base64 is: An encoding scheme that converts binary data to ASCII text using 64 characters. Recognizable by = padding at the end.

Critical security point: base64 is NOT encryption. Anyone can decode it instantly. Attackers use it to obfuscate payloads in scripts and hide commands in phishing emails. It looks mysterious to non-technical people but one command undoes it.

## Bandit Level 10 to 11: ROT13

Command used:
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'

ROT13 rotates every letter 13 positions in the alphabet. Not encryption. Not security. Appears frequently in CTF challenges. The tr command translates character sets — useful for text transformation tasks in security scripts.

---

## Python Dictionaries

A dictionary stores key-value pairs. Look something up by key, get the associated value.

services = {21: "FTP", 22: "SSH", 80: "HTTP", 443: "HTTPS", 3389: "RDP"}
print(services[22])  — prints: SSH

For port numbers, a dictionary is the natural structure. Port number is the key. Service name is the value.

### Port Scanner Upgrade

Added common_services dictionary to port_scanner.py. Changed output from:
[OPEN]   Port 22
to:
[OPEN]   Port 22 — SSH

Used dictionary get method with default value:
service = common_services.get(port, "Unknown")

If port is in the dictionary, returns the service name. If not, returns "Unknown" without crashing.

Uploaded the updated version to python-security-tools as version 2. Commit message: v2: Added service name lookup using dictionary.

---

## Commands Used Today

cat data.txt | base64 --decode
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
ssh bandit10@bandit.labs.overthewire.org -p 2220
