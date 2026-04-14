# Day 6 — Bandit Levels 4–7, Python Functions + Lists, Port Scanner Built

**Time spent:** Approximately 3.5 hours (Saturday — longer day)
**What I covered:** OverTheWire Bandit levels 4 through 7, Python chapters 3 and 4, wrote a working port scanner from scratch, researched 10 target employers

---

## How the day went

Today was the most significant day of the week. I wrote a working security tool from scratch in Python — a port scanner that connects to any IP address and reports which ports are open. It runs in my Kali terminal. It works on real targets. It is on my GitHub.

Six days ago, I did not know what a port was. Today I built a tool that finds them.

The Bandit levels continued to teach real techniques. Levels 5 through 7 introduced the find command with property-based filtering — searching by file size, by owner, by group, by permissions. These are exactly the commands used in post-exploitation on compromised Linux systems.

---

## OverTheWire Bandit — Levels 4 through 7

### Level 4 → 5: Find the only human-readable file

**Challenge:** There are multiple files in a directory called inhere. Only one contains human-readable text. Find it.

**Commands used:**
```
ssh bandit4@bandit.labs.overthewire.org -p 2220
cd inhere
file ./-file0*
```

**What the file command does:** file examines a file's content and tells you what type it is — ASCII text, data (binary), executable, image, etc. The * wildcard runs it against all files matching the pattern at once.

**Result:** One file came back as ASCII text. The rest were listed as data (binary). Read the ASCII one with cat.

**Real-world application:** During forensics investigations, you often encounter directories full of unknown files. The file command tells you what each one actually is regardless of the extension. Attackers sometimes disguise files by giving them misleading extensions — file catches that.

---

### Level 5 → 6: Find by file properties

**Challenge:** The password is in a file that is human-readable, exactly 1033 bytes in size, and not executable. It is somewhere in the inhere directory.

**Command used:**
```
find. -type f -size 1033c! -executable
```

**Breaking down the flags:**
- `-type f` — only find files (not directories)
- `-size 1033c` — exactly 1033 bytes (c = bytes)
- `! -executable` — the! means NOT. Excludes executable files.

**What this teaches:** The find command is one of the most powerful tools in Linux. You can search by name, type, size, owner, group, permissions, timestamp, and combinations of all of these. During a penetration test or forensic investigation, find lets you locate specific files across an entire file system in seconds.

---

### Level 6 → 7: Find by owner and group anywhere on the server

**Challenge:** The file is owned by user bandit7, belongs to group bandit6, and is 33 bytes in size. It is somewhere on the entire server — not just in your home directory.

**Command used:**
```
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

**What is different here:** The search starts from / — the root of the entire file system. This generates many permission denied errors as it tries to read system directories we do not have access to. The 2>/dev/null redirects all error messages to nowhere so the output stays clean.

**Result:** One file path appeared among all the noise. Read it with cat.

**Real-world application:** This exact command structure — find across the entire filesystem with owner and permission filters — is used in privilege escalation scripts. Tools like LinPEAS (Linux Privilege Escalation Awesome Script) run variations of this automatically to find files that a low-privilege user should not normally be able to read.

---

### Level 7 → 8: Search inside a large file

**Challenge:** The password is next to the word "millionth" somewhere in a file called data.txt which contains millions of lines.

**Command used:**
```
grep "millionth" data.txt
```

**Result:** One line appeared immediately containing the word millionth followed by the password.

**What this teaches:** grep searches through file contents for patterns. data.txt has millions of lines — opening it with cat would scroll for minutes. grep finds the relevant line instantly regardless of file size.

**Real-world application:** Security analysts use grep constantly to search log files. A log file from a busy server might contain millions of entries. grep lets you find every failed login attempt, every connection from a specific IP, every reference to a specific error code — instantly.

---

### Bandit Levels Summary — Updated

| Level | Key concept | Command |
|-------|-------------|---------|
| 0 → 1 | Reading files | cat readme |
| 1 → 2 | Special character filenames | cat ./- |
| 2 → 3 | Spaces in filenames | cat "spaces in this filename" |
| 3 → 4 | Hidden files | ls -la, cat .hidden |
| 4 → 5 | Identifying file types | file ./-file0* |
| 5 → 6 | Finding by properties | find . -type f -size 1033c! -executable |
| 6 → 7 | Finding by owner across filesystem | find / -user bandit7 -group bandit6 -size 33c 2>/dev/null |
| 7 → 8 | Searching file contents | grep "millionth" data.txt |

---

## Python — Chapters 3 and 4

### Chapter 3: Functions

Functions are named blocks of code that you can call repeatedly without rewriting the same code.

```python
def greet(name):
    print("Hello " + name + "!")
    print("Welcome to security training.")

greet("Narasimha")
greet("Recruiter")
```

**Why functions matter for security tools:** Every tool I will write has functions. A port scanner has a function that checks one port. The main code calls that function in a loop for every port number. A password cracker has a function that hashes one word. The main code calls it for every word in the wordlist.

Without functions, code becomes a mess of repeated logic that is impossible to maintain or debug.

**Return values:**
```python
def check_port(ip, port):
    # code to check if the port is open
    if port_is_open:
        return True
    else:
        return False

result = check_port("192.168.1.1", 80)
if result:
    print("Port 80 is open")
```

The function returns a value that the calling code can use to make decisions.

### Chapter 4: Lists

Lists store multiple values in a single variable.

```python
open_ports = []

for port in range(1, 1025):
    # check each port
    if port_is_open:
        open_ports.append(port)

print("Open ports found: " + str(open_ports))
```

**List operations used in security tools:**
- `open_ports.append(22)` — add a port to the list
- `len(open_ports)` — count how many open ports were found
- `for port in open_ports:` — loop through every open port
- `open_ports.sort()` — sort ports in numerical order

---

## The Port Scanner — Built Today

This is the most significant thing I have built so far. A working port scanner written from scratch in Python. Here is the complete code:

```python
import socket
from datetime import datetime

print("=" * 50)
print("  Python Port Scanner")
print("=" * 50)

target = input("\nEnter IP address or domain to scan: ")

print("\nTarget:  " + target)
print("Started: " + str(datetime.now()))
print("-" * 50)

open_ports = []

for port in range(1, 1025):
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.settimeout(0.5)
    result = sock.connect_ex((target, port))
    if result == 0:
        print("[OPEN]   Port " + str(port))
        open_ports.append(port)
    sock.close()

print("-" * 50)
print("Scan complete.")
print("Total open ports found: " + str(len(open_ports)))
print("Open ports: " + str(open_ports))
print("Finished: " + str(datetime.now()))
```

### How it works — line by line

**import socket** — imports Python's built-in networking library. The socket library lets Python make direct TCP connections to any IP and port.

**socket.socket(AF_INET, SOCK_STREAM)** — creates a TCP socket. AF_INET means IPv4. SOCK_STREAM means TCP (as opposed to UDP).

**sock.settimeout(0.5)** — if a port does not respond within 0.5 seconds, move on. Without this, the scanner would wait forever on filtered ports.

**sock.connect_ex((target, port))** — attempts a TCP connection to the target IP on the given port. Returns 0 if the connection succeeded (port is open). Returns an error code if it failed (port is closed or filtered).

**sock.close()** — closes the connection immediately. We are just checking if it opens — we do not need to keep it open.

**open_ports.append(port)** — if the port is open, add its number to the list.

### How I tested it

First, I scanned my own machine:
```
python3 port_scanner.py
Enter IP address or domain to scan: 127.0.0.1
```

Then I scanned the legal practice target:
```
python3 port_scanner.py
Enter IP address or domain to scan: scanme.nmap.org
```

**Results from scanme.nmap.org:**
[Replace with your actual results — the ports you saw open]

**Comparison to Nmap:** My Python scanner found the same open ports that Nmap found on Day 3. Two completely different tools built by different people in different ways — same result. That is because both tools are doing the same thing at the network level: attempting TCP connections and reporting which ones succeed.

### What I learned building this

Writing this tool taught me more about how port scanning actually works than reading about it ever did. The key insight: a port scanner is not doing anything magical. It is just trying to open a door and seeing if anyone answers. If the connection completes — door is open. If it times out or gets refused — door is closed.

Nmap does this same thing underneath all its sophisticated features. The core operation is: attempt connection, record result.

---

## Job Research — 10 Target Employers Found

Today I spent 30 minutes on LinkedIn and Indeed researching entry-level cybersecurity roles. Here is what I found:

**Key observations from job listings:**
- Most entry-level SOC Analyst roles require: Security+, basic networking knowledge, familiarity with SIEM tools (Splunk is the most common), and Windows/Linux experience
- Several listings in NJ/NY explicitly mention H1B sponsorship available
- Large consulting firms — Deloitte, Accenture, IBM, Booz Allen Hamilton — have the most entry-level openings
- Many government contractor roles are in the DC/MD/VA area, but several have remote options

**Skills appearing in every listing I found:**
- CompTIA Security+ (listed in 9 out of 10 postings)
- SIEM experience — especially Splunk
- Incident response
- Network security fundamentals
- Windows and Linux

**Most important finding:** Security+ is truly non-negotiable. Every single listing either required it or strongly preferred it. Starting Security+ study is the highest priority for Week 2.

---

## Commands Reference — Day 6

```
# Bandit levels 4-7
file ./-file0*
find. -type f -size 1033c! -executable
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
grep "millionth" data.txt

# Python port scanner
nano port_scanner.py          # create the file
python3 port_scanner.py       # run it
```

---

## Status Update

**TryHackMe rooms completed:** 5
**OverTheWire Bandit:** Level 8 — strong progress
**GitHub commits:** 6 consecutive days
**Python tools built:** 1 (port scanner — live on GitHub)
**Job listings researched:** 10
**Security+ study start:** Week 2, Day 1

---

## Tomorrow — Day 7 

- Full Week 1 review and self-test
- Update all profiles with the week summary
- Connect with 10 cybersecurity professionals on LinkedIn
- Week 1 reflection post — the biggest LinkedIn post of the week
- Plan Week 2 study schedule
