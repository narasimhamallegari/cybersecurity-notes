# Day 11 — Social Engineering, Netcat, Password Generator Built

Date: April 13, 2026
Time spent: 2 hours

---

## Security+ Domain 2 — Social Engineering Attacks

### Phishing
Mass email campaign pretending to be a legitimate organization. Sent to thousands simultaneously. Low success rate per person, high in aggregate.

### Spear Phishing
Targeted phishing aimed at a specific person using personal details — name, employer, role. Much more effective than generic phishing.

### Whaling
Spear phishing targeting executives — CEO, CFO, CISO. High value targets.

### Vishing
Voice phishing. Phone calls pretending to be IT support, banks, or the government.

### Smishing
SMS phishing. Malicious links via text message. Higher trust than email.

### Pretexting
Creating a fabricated scenario to manipulate a target. Example: calling as a new IT employee asking for credentials to "verify the system."

### Baiting
Leaving malware-infected USB drives in parking lots. High success rate — studies show most people plug in found drives.

### Tailgating / Piggybacking
Following an authorized person through a secure door without credentials. Exhibits social courtesy.

### Indicators to recognize
Urgency, authority claims, intimidation, scarcity, social proof, and excessive familiarity.

---

## Bandit Level 14 to 15: SSH Keys and Netcat

### SSH Key Authentication
Used ssh -i sshkey. private to log in using a private key file instead of a password.
SSH keys: private key on your machine, public key on the server. Whoever has the private key logs in without a password. Stolen SSH keys = persistent backdoor in cloud environments.

### Netcat (nc)
echo "password" | nc localhost 30000

Raw TCP connection to any host and port. Called the Swiss Army knife of networking. Used for: testing port connectivity, creating reverse shells, file transfers, and banner grabbing.

### OpenSSL SSL Connection
echo "password" | openssl s_client -connect localhost:30001 -quiet

Opens an encrypted SSL/TLS connection. Used to test SSL certificates and verify encrypted services.

---

## Password Generator — Second Python Security Tool

import random
import string

length = int(input("Password length (minimum 12): "))
if length < 12:
    length = 12
count = int(input("How many passwords? "))

for i in range(count):
    chars = string.ascii_letters + string.digits + string.punctuation
    password = ''.join(random.choice(chars) for x in range(length))
    print(str(i + 1) + ". " + password)

print("Store in a password manager. Never reuse.")

random. choice selects each character independently. Result meets complexity requirements — uppercase, lowercase, digits, symbols. Unpredictable by design.

Uploaded to python-security-tools. Two tools now live.

---

## Commands Used Today

nc localhost 30000
openssl s_client -connect localhost:30001 -quiet
ssh -i sshkey.private bandit14@localhost -p 2220
