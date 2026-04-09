# Day 5 — DNS Records, HTTP Deep Dive, Python Begins, First Script Written

**Date:** April 8, 2026
**Time spent:** Approximately 2 hours
**What I covered:** Professor Messer DNS Record Types video, TryHackMe DNS in Detail + HTTP in Detail rooms, Python Automate the Boring Stuff chapters 1 and 2, wrote and ran the first Python script

---

## How the day went

Today was the first day when three completely different skills came together in one session. DNS and HTTP are network-level concepts. Python is a programming language. They feel unrelated until you realize that the tools I will eventually write in Python will query DNS records and make HTTP requests automatically. Today was planting seeds for that.

The Python chapter was the part I was most nervous about going into. I have never written code before. But chapters 1 and 2 of Automate the Boring Stuff are genuinely accessible — Al Sweigart writes like he is sitting next to you, not like he is writing a textbook. By the end of two chapters, I had written a working script and run it in my Kali terminal. That felt significant.

---

## DNS Record Types — Full Reference

I already covered what DNS does on Day 3. Today was specifically about the different record types and what each one stores. This matters for security because, during reconnaissance, attackers query all of these records to map a target organization.

### A Record — Address
Maps a domain name to an IPv4 address.
```
nslookup google.com
```
Returns: 142.250.80.46 (and several others — Google has many servers)

This is the most basic DNS record. It answers the question: "What IP address do I connect to when I type this domain?"

**Security relevance:** An A record lookup is always the first step of DNS reconnaissance. It gives you the target's IP address — the starting point for everything else.

---

### AAAA Record — IPv6 Address
Maps a domain name to an IPv6 address. Same concept as an A record, but for the newer address format.
```
nslookup -type=AAAA google.com
```

**Security relevance:** Many security tools and monitoring systems focus on IPv4 and miss IPv6 traffic entirely. Attackers sometimes use IPv6 to bypass IPv4-focused defenses.

---

### MX Record — Mail Exchange
Identifies which servers handle email for a domain. Has a priority number — lower number means higher priority.
```
nslookup -type=MX google.com
```
Returns: mail servers with priority values like 10, 20, 30

**Security relevance:** MX records are used in email-based attacks. If you know which mail server handles email for a company, you can target it directly. Phishing infrastructure setup also uses MX records. SPF and DKIM records (stored as TXT) are the defenses against email spoofing — and they are all in DNS.

---

### CNAME Record — Canonical Name
An alias — points one domain name to another domain name rather than directly to an IP address.
Example: www.example.com might be a CNAME pointing to example.com

**Security relevance:** Subdomain takeover attacks exploit dangling CNAME records. If example.com has a CNAME pointing to a third-party service like myapp.github.io, and that GitHub pages site no longer exists, an attacker can claim that GitHub Pages URL and now controls what visitors to www.example.com see. This is a real attack vector that has affected major companies.

---

### NS Record — Name Server
Identifies which servers are authoritative for a domain's DNS. These servers have the definitive answers for all DNS queries about that domain.
```
nslookup -type=NS google.com
```
Returns: ns1.google.com, ns2.google.com, ns3.google.com, ns4.google.com

**Security relevance:** Compromising a nameserver is one of the highest-impact attacks possible — it allows redirecting ALL traffic for a domain. DNS hijacking at the NS level has been used in nation-state cyberattacks to intercept traffic from entire countries.

---

### TXT Record — Text
Stores arbitrary text data. Used for many purposes, including domain ownership verification and email security configurations.
```
nslookup -type=TXT google.com
```

Common uses:
- SPF records — define which mail servers are authorized to send email for a domain
- DKIM keys — cryptographic keys used to sign outgoing email
- Domain verification for Google, Microsoft, and other services
- DMARC policies — how mail servers should handle email that fails authentication

**Security relevance:** TXT records often reveal which third-party services a company uses. Attackers enumerate TXT records to understand a target's technology stack before crafting targeted attacks.

---

### PTR Record — Pointer
Reverse DNS — maps an IP address back to a domain name. The opposite of an A record.
```
nslookup 8.8.8.8
```
Returns: dns.google (the domain name for Google's 8.8.8.8 DNS server)

**Security relevance:** Used in network forensics and incident response. When you see a suspicious IP in a log, a PTR lookup tells you what domain it belongs to. Also used to verify mail server legitimacy — a mail server without a valid PTR record is often flagged as spam.

---

### DNS Record Summary Table

| Record | Maps | Example | Security use |
|--------|------|---------|--------------|
| A | Domain → IPv4 | google.com → 142.250.80.46 | Initial recon target |
| AAAA | Domain → IPv6 | google.com → 2607:f8b0::... | IPv6 bypass techniques |
| MX | Domain → Mail server | google.com → smtp.google.com | Email attack planning |
| CNAME | Domain → Domain | www → example.com | Subdomain takeover |
| NS | Domain → Nameserver | google.com → ns1.google.com | DNS hijacking target |
| TXT | Domain → Text data | SPF, DKIM, DMARC | Technology stack mapping |
| PTR | IP → Domain | 8.8.8.8 → dns.google | Forensics and attribution |

---

## HTTP in Detail — TryHackMe Room Completed

**Room:** tryhackme.com/room/httpindetail
**Status:** 100% complete

HTTP (HyperText Transfer Protocol) is the protocol used by web browsers and web servers to communicate. Everything you do on a website — loading a page, submitting a form, logging in — is an HTTP request and response.

### HTTP Methods

| Method | What it does | Security note |
|--------|-------------|---------------|
| GET | Retrieve data from a server | Parameters are in the URL — visible in logs |
| POST | Send data to a server | Data in request body — not in URL |
| PUT | Update existing data | Can overwrite resources if improperly secured |
| DELETE | Remove data | Dangerous if not authenticated properly |
| PATCH | Partially update data | Less common but same risks as PUT |

**Why methods matter for security:** Web application vulnerabilities often come from improperly handling these methods. A server that accepts DELETE requests without verifying authentication is a critical vulnerability. A server that processes PUT requests from any user could allow unauthorized data modification.

### HTTP Status Codes

| Code | Meaning | Security relevance |
|------|---------|-------------------|
| 200 | OK — request succeeded | Normal |
| 301 | Moved permanently | Redirect — can be exploited for open redirect attacks |
| 302 | Found — temporary redirect | Same redirect risks |
| 400 | Bad request | Often reveals server is receiving unexpected input |
| 401 | Unauthorized | Authentication required |
| 403 | Forbidden | Authenticated but not authorized — access control issue |
| 404 | Not found | Directory enumeration tools look for 200s not 404s |
| 500 | Internal server error | Can reveal stack traces with sensitive information |
| 503 | Service unavailable | Target of DoS attacks |

**The 403 vs 401 distinction matters:** 401 means you need to log in. 403 means you are logged in but not allowed. If a user can change a 403 to a 200 by manipulating the request, that is a broken access control vulnerability — one of the most common web application security issues.

### HTTP Headers

Headers are metadata sent with every request and response. They contain information about the browser, the server, authentication tokens, and caching instructions.

**Important request headers:**
- Host — which website you are requesting (important on shared hosting)
- User-Agent — your browser and operating system
- Cookie — session tokens and authentication data
- Authorization — credentials or tokens
- Content-Type — format of data being sent

**Important response headers:**
- Server — web server software (Apache, nginx, IIS) and sometimes a version
- Content-Type — format of data being returned
- Set-Cookie — server is setting a cookie in your browser
- Strict-Transport-Security — forces HTTPS
- X-Frame-Options — prevents clickjacking

**Security relevance of headers:** The Server response header tells attackers what web server software and version is running — same information that Nmap -sV provides. Many servers are misconfigured to expose this. Security best practice is to hide or change the Server header. Also: cookies without the HttpOnly flag can be stolen by JavaScript (XSS attacks).

### Cookies — what they are and why they are attacked

When you log into a website, the server creates a session and sends you a cookie — a small piece of data your browser stores and sends back with every subsequent request. This is how the server knows you are still logged in.

Cookie security flags:
- **HttpOnly** — cookie cannot be accessed by JavaScript. Prevents XSS cookie theft.
- **Secure** — cookie is only sent over HTTPS. Prevents interception on HTTP.
- **SameSite** — cookie is only sent to same origin. Prevents CSRF attacks.

A session cookie without HttpOnly set is vulnerable to cross-site scripting. If an attacker injects JavaScript into a page you visit, that script can read your session cookie and send it to the attacker. The attacker then uses your cookie to impersonate you — they are logged in as you without knowing your password.

This is session hijacking. It is a real attack. HttpOnly being set on session cookies prevents it entirely.

---

## Python — Chapters 1 and 2 of Automate the Boring Stuff

**Source:** automatetheboringstuff.com — completely free online

I was nervous about starting Python because I had never written code before. Two chapters in, that nervousness is mostly gone. Python reads more like English than I expected and the book by Al Sweigart does not assume any prior knowledge.

### What Chapter 1 taught me

```python
>>> 2 + 2
4
>>> "Hello" + " cybersecurity"
'Hello cybersecurity'
>>> x = 10
>>> name = "Narasimha"
>>> print("Hello " + name)
Hello Narasimha
>>> type(x)

>>> type(name)

```

Variables store values. Strings are text wrapped in quotes. Integers are numbers. The type() function tells you what kind of data something is. print() outputs to the screen.

The most important thing I learned in Chapter 1: Python is interpreted line by line. When something goes wrong, the error message tells you exactly which line and what the problem is. This makes debugging much more approachable than I expected.

### What Chapter 2 taught me

```python
# If statements
age = 25
if age >= 18:
    print("Adult")
else:
    print("Minor")

# For loop
for port in range(1, 6):
    print("Checking port " + str(port))

# While loop
attempts = 0
while attempts < 3:
    password = input("Enter password: ")
    if password == "secret":
        print("Access granted")
        break
    attempts = attempts + 1
    print("Wrong. Attempts remaining: " + str(3 - attempts))
```

If statements make decisions. For loops repeat a block of code a set number of times. While loops repeat until a condition is no longer true. Break exits a loop immediately.

**Why this matters for security tools:** Every security tool is fundamentally a loop. A port scanner loops through port numbers and checks each one. A password cracker loops through a wordlist. A directory brute forcer loops through paths. Chapter 2 is the conceptual foundation for all of that.

---

## First Python Script — Written and Run Today

I wrote my first complete Python script in Kali Linux using the nano text editor. Here it is:

```python
name = input("What is your name? ")
print("Welcome to cybersecurity training, " + name + "!")
print("")

for i in range(1, 6):
    print("Loading security module " + str(i) + "...")

print("")
print("All modules loaded. Training begins now.")
print("Today is Day 5. Keep going.")
```

**How I ran it:**
```
nano first_script.py
# typed the code above
# Ctrl+X then Y then Enter to save
python3 first_script.py
```

**What it outputs:**
```
What is your name? Narasimha
Welcome to cybersecurity training, Narasimha!

Loading security module 1...
Loading security module 2...
Loading security module 3...
Loading security module 4...
Loading security module 5...

All modules loaded. Training begins now.
Today is Day 5. Keep going.
```

This script does nothing useful from a security standpoint. But it uses everything from chapters 1 and 2: variables, input(), print(), string concatenation, a for loop with range(), and str() to convert a number to a string. Writing it yourself — not copying it — is what builds the muscle memory.

---

## Commands and Code Reference — Day 5

```
# DNS record queries
nslookup -type=MX google.com
nslookup -type=NS google.com
nslookup -type=TXT google.com
nslookup -type=AAAA google.com
nslookup 8.8.8.8              # reverse lookup (PTR)
dig google.com TXT
dig google.com MX

# Python commands in Kali
python3                        # open Python interpreter
python3 first_script.py       # run a script file
nano first_script.py          # create/edit script in terminal
```

---

## What I noticed about learning Python

The book says to type every example yourself. I almost skipped that and just read. I did not skip it, and I am glad. The moment I started making typos and seeing error messages and fixing them, I was actually learning. Reading the code without running it teaches you almost nothing about what it actually does.

The error messages in Python are more helpful than I expected. When I forgot a closing parenthesis, it said:
```
SyntaxError: '(' was never closed
```
Exactly which line? Exactly what the problem is. That is not confusing — that is the compiler helping you.

---

## Questions I am carrying forward

- What is the difference between single quotes and double quotes in Python?
- How does Python handle errors gracefully without crashing? What is a try/except block?
- What is the socket library, and how does it let Python talk to networks?
- How do I read from and write to files in Python?

---

## Status Update

**TryHackMe rooms completed:** 5 (Networking, OSI Model, DNS in Detail, HTTP in Detail, Linux Fundamentals Part 1)
**OverTheWire Bandit:** Level 4 — continuing tomorrow
**GitHub commits:** 5 consecutive days
**Python:** Chapters 1 and 2 done, first script written and running
**LinkedIn skills added:** Network Security, Wireshark, Nmap, DNS, Linux, Bash, Kali Linux, Python

---

## Tomorrow — Day 6

- OverTheWire Bandit levels 4 through 7
- Python chapters 3 and 4 (functions and lists)
- Write Python port scanner — first real security tool
- Create python-security-tools GitHub repository
- Job research — find 10 target employers
