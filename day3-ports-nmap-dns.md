# Day 3 — Common Ports, Nmap Scanning, DNS Recon, TryHackMe OSI Model Room

**Date:** April 5, 2026
**Time spent:** Approximately 2 hours
**What I covered:** Professor Messer Common Ports + Introduction to IP videos, first Nmap scans in Kali Linux, DNS recon commands, TryHackMe OSI Model room

---

## How the day went

Today was the first day I felt like I was actually doing security work rather than just studying it. Running Nmap against a real server and seeing open ports appear in my terminal — ports I now know by number from this morning's video — made everything connect in a way that reading notes never does.

The DNS recon commands especially surprised me. I had no idea how much information about a company or domain is sitting publicly accessible in DNS records. Anyone can query it. Attackers absolutely do.

---

## What I learned: Common Ports

Ports are numbered doors on a server. Each service listens on a specific door. When you connect to a website on port 443, your browser knocks on door number 443 and the HTTPS service answers. If no service is listening on that port, the connection is refused.

Knowing ports by memory is not optional in cybersecurity. Every Nmap scan, every firewall rule, every incident report references port numbers. These are the ones I memorized today:

**Critical ports — must know these cold:**

| Port | Protocol | What it does | Security note |
|------|----------|--------------|---------------|
| 21 | FTP | File transfer | Unencrypted — credentials sent in plain text |
| 22 | SSH | Secure remote terminal | Encrypted — brute force target |
| 23 | Telnet | Remote terminal | Completely unencrypted — never use |
| 25 | SMTP | Sending email | Can be abused for spam relay |
| 53 | DNS | Domain name resolution | UDP + TCP — DNS poisoning target |
| 80 | HTTP | Web traffic | Unencrypted — never use for sensitive data |
| 110 | POP3 | Email download | Older protocol — legacy systems |
| 143 | IMAP | Email sync | More modern than POP3 |
| 443 | HTTPS | Web traffic encrypted | TLS — standard for all serious websites |
| 445 | SMB | Windows file sharing | EternalBlue exploit lives here — caused WannaCry |
| 3306 | MySQL | Database connections | Should NEVER be exposed to the internet |
| 3389 | RDP | Windows Remote Desktop | Major brute force and ransomware target |
| 8080 | HTTP-alt | Dev servers, proxies | Often misconfigured and exposed |

**Why attackers care about ports:**
An attacker who runs a port scan and finds port 3389 open knows that Windows Remote Desktop is exposed. They can try brute forcing the login. This is exactly how a significant percentage of ransomware attacks start — exposed RDP with weak passwords. Knowing ports means understanding attack surfaces before the attackers do.

---

## What I learned: IP Addresses

An IP address is a unique numerical label assigned to every device on a network. It is the return address and the destination address for every packet.

**IPv4:**
- 32-bit number written as four groups separated by dots
- Example: 192.168.1.1
- Approximately 4.3 billion possible addresses
- We have run out — which is why IPv6 exists

**IPv6:**
- 128-bit number written in hexadecimal groups separated by colons
- Example: 2001:0db8:85a3:0000:0000:8a2e:0370:7334
- Virtually unlimited addresses — 340 undecillion possible combinations
- Adoption is happening but IPv4 is still dominant in most environments

**Private vs Public IP ranges:**

Private IPs (used inside local networks — not routable on the internet):
- 10.0.0.0 to 10.255.255.255
- 172.16.0.0 to 172.31.255.255
- 192.168.0.0 to 192.168.255.255

Public IPs are assigned by ISPs and are visible on the open internet. When you connect to a website, the site sees your public IP — not your private local IP.

**Security relevance:** During reconnaissance, an attacker tries to identify the IP range a company owns. Tools like Shodan.io let anyone search for internet-connected devices by IP. Understanding IP structure is essential for both attack planning and network defense.

---

## Nmap — First Real Security Scans

Nmap (Network Mapper) is the most widely used reconnaissance tool in cybersecurity. Every penetration test starts with Nmap. Every security assessment uses it. It is free, powerful, and already installed in Kali Linux.

I ran all scans today against scanme.nmap.org — a server that Nmap's creators provide specifically for students and researchers to practice legally.

### Scan 1 — Basic port scan of my own machine

```
nmap localhost
```

**What this does:** Scans the most common 1,000 ports on my own Kali machine.

**Result I got:**
```
Starting Nmap 7.94 at 2026-04-05
Nmap scan report for localhost (127.0.0.1)
PORT   STATE SERVICE
22/tcp open  ssh
All 999 scanned ports are closed
```

Port 22 (SSH) is open on my Kali machine. That makes sense — SSH is how you remotely access a Linux machine, and Kali has it enabled by default.

### Scan 2 — Scan a real server

```
nmap scanme.nmap.org
```

**What this does:** Scans the top 1,000 most common ports on a real public server.

**Result I got:**
```
PORT      STATE    SERVICE
22/tcp    open     ssh
80/tcp    open     http
9929/tcp  open     nping-echo
31337/tcp open     Elite
```

Port 80 is open — this server runs a web server on HTTP. Port 22 is open — SSH access is available. Seeing this on a real server made the concept of attack surface tangible. Every open port is a potential entry point.

### Scan 3 — Version detection (finding what software is actually running)

```
nmap -sV scanme.nmap.org
```

**What the -sV flag does:** Instead of just telling you a port is open, Nmap tries to identify the exact software and version running on it.

**Why this matters for security:** If Nmap reveals that a server is running Apache 2.4.49 for example, an attacker can look up known vulnerabilities for that exact version. This is called version-specific exploitation — and it is extremely common in real attacks.

**Result I got:**
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 6.6.1p1
80/tcp open  http    Apache httpd 2.4.7
```

### Scan 4 — Scan specific ports only

```
nmap -p 22,80,443 scanme.nmap.org
```

**What this does:** Only checks those three specific ports rather than all 1,000. Useful when you already know what you are looking for. Faster and quieter.

### Scan 5 — Save results to a file

```
nmap -sV scanme.nmap.org -oN day3-nmap-results.txt
cat day3-nmap-results.txt
```

**What -oN does:** Saves the output in normal human-readable format to a text file. In a real penetration test you always save your results. Evidence and documentation are critical.

---

## DNS Recon Commands

DNS (Domain Name System) is a public database that maps domain names to IP addresses and stores other records about a domain. The important thing security-wise: it is completely public. Anyone can query it. Attackers always query it during the reconnaissance phase.

I ran all of these in my Kali terminal today:

### nslookup — basic DNS lookup

```
nslookup google.com
```
**Result:** Returned 142.250.80.46 and several other IPs (Google has many servers).
**What this tells an attacker:** The IP addresses where the domain resolves — first step in mapping a target.

```
nslookup -type=MX google.com
```
**Result:** Returns Google's mail servers — smtp.google.com and others.
**What this tells an attacker:** Which mail servers handle email for this domain. Useful for email-based attacks and phishing setup.

```
nslookup -type=NS google.com
```
**Result:** Returns ns1.google.com, ns2.google.com, ns3.google.com, ns4.google.com
**What this tells an attacker:** Which nameservers control DNS for this domain. A nameserver compromise would allow complete DNS hijacking.

### dig — detailed DNS tool

```
dig google.com
```
**Result:** More detailed output than nslookup — includes the full DNS response, query time, and which DNS server answered.

```
dig google.com MX
```
**Result:** Mail exchange records with priority numbers. Lower number = higher priority server.

**Why dig is used in security work:** dig gives you the raw DNS response in full detail. When investigating a suspicious domain, dig reveals more than nslookup. It is the preferred tool for professional DNS analysis.

### host — quick and clean lookup

```
host google.com
```
**Result:** Clean one-line output: "google.com has address 142.250.80.46"

**When to use host vs dig:** host for quick checks, dig for detailed investigation.

---

## DNS Record Types — what each one means

| Record | Full name | What it stores | Security relevance |
|--------|-----------|----------------|-------------------|
| A | Address | Domain to IPv4 address | Primary attack mapping target |
| AAAA | IPv6 Address | Domain to IPv6 address | Less commonly targeted |
| MX | Mail Exchange | Which servers handle email | Phishing and email spoofing |
| CNAME | Canonical Name | Domain alias to another domain | Subdomain takeover attacks |
| NS | Name Server | Authoritative DNS server | DNS hijacking target |
| TXT | Text | Free text — used for verification | Reveals domain ownership and services |
| PTR | Pointer | IP to domain (reverse lookup) | Used in network forensics |

---

## TryHackMe — OSI Model Room Completed

**Room:** tryhackme.com/room/osimodelzi
**Status:** 100% complete

This room tested everything from Day 1 notes. A few questions that made me think:

**Question type I found hardest:** Identifying which layer a specific attack targets. For example, ARP spoofing. I initially thought Layer 3 because ARP maps IP addresses. But ARP actually operates at Layer 2 (Data Link) because it maps IP addresses to MAC addresses, and MAC addresses are a Layer 2 concept.

**The lesson:** Some concepts cross layers, and you have to think carefully about exactly where the operation happens, not just what it involves.

**Score first attempt:** 14 out of 16 correct. Two wrong answers both involved the boundary between Layer 2 and Layer 3 — something I need to review.

---

## What Today Taught Me About Real Security Work

The combination of Nmap and DNS recon I did today is literally the first 30 minutes of every professional penetration test. Before an attacker touches anything, they:

1. Run DNS queries to map the domain's infrastructure
2. Run Nmap to find open ports on discovered IP addresses
3. Use version detection to identify vulnerable software versions
4. Document everything for later exploitation

I did all four steps today on a legal target. The same commands, the same workflow. The only difference between what I did and what a professional does is the target and the authorization.

---

## Commands Reference — Day 3

```
# Nmap commands
nmap localhost
nmap scanme.nmap.org
nmap -sV scanme.nmap.org
nmap -p 22,80,443 scanme.nmap.org
nmap -sV scanme.nmap.org -oN results.txt

# DNS recon commands
nslookup google.com
nslookup -type=MX google.com
nslookup -type=NS google.com
dig google.com
dig google.com MX
host google.com
```

---

## Questions I Am Carrying Forward

- What does Nmap do differently with a SYN scan (-sS) vs a connect scan (-sT)?
- How does an attacker use the open port information from Nmap to plan the next step?
- What is DNS zone transfer, and why is it a security vulnerability when misconfigured?
- How do I find subdomains of a domain during reconnaissance?

---

## Status Update

**TryHackMe rooms completed:** 3 (What is Networking, OSI Model, DNS in Detail — Day 2)
**OverTheWire Bandit:** Starting Day 4
**GitHub commits this week:** 3 consecutive days
**Security+ study:** Beginning formal study Week 2

---

## Tomorrow — Day 4

- linuxjourney.com Grasshopper course (Linux fundamentals)
- OverTheWire Bandit levels 0 through 3
- TryHackMe Linux Fundamentals Part 1
- Practice: ls, cd, cat, find, grep, chmod in Kali terminal
