# Day 8 — Security+ SY0-701 Study Begins + Bandit Levels 8 and 9

Date: April 10, 2026
Time spent: 2.5 hours
Source: Professor Messer SY0-701 free course + OverTheWire Bandit

---

## How the day went

Week 2 Day 1. Security+ is now the daily non-negotiable. Exam booked for June 9, 2026. Having a real date changes how studying feels — it is no longer open-ended preparation, it is a countdown.

Professor Messer's SY0-701 course is free on his website at professormesser.com. Videos are organized by exam domain. Going through them in order and taking detailed notes on every new term.

---

## Security+ Domain 1 — Key Concepts Covered

### The CIA Triad

Every security decision maps back to one or more of these three principles:

Confidentiality: Only authorized people can access data.
Attacks: data breaches, eavesdropping, unauthorized access.

Integrity: Data has not been tampered with or modified.
Attacks: man-in-the-middle modification, data corruption, unauthorized changes.

Availability: Systems are accessible when legitimate users need them.
Attacks: DDoS attacks, ransomware, and hardware failures.

### Authentication vs Authorization

Authentication: Confirming who you are. Proving your identity. Username and password, fingerprint, one-time code.

Authorization: Determining what you are allowed to do after your identity is confirmed. Access control lists, role-based permissions, privilege levels.

Why the distinction matters: A system can authenticate you perfectly and still let you access things you should not because authorization is broken. Broken access control is in the OWASP Top 10 every year.

### Security Control Types

Preventive: Stops attacks before they happen. Examples: firewall, encryption, locked door.
Detective: Identifies attacks as they happen or after. Examples: SIEM alerts, IDS, and log monitoring.
Corrective: Fixes damage after an attack. Examples: restoring from backup, patching.
Deterrent: Discourages attacks without physically stopping them. Examples: visible cameras, warning signs.
Compensating: Alternative control when primary is not possible. Example: extra monitoring when a patch cannot be applied.

### Authentication Factors

Something you know: Password, PIN.
Something you have: Phone, hardware token, smart card.
Something you are: Fingerprint, retina scan, face recognition.
Somewhere you are: Geolocation-based access.
Something you do: Typing pattern, behavioral biometrics.

MFA requires two or more factors from different categories. Two passwords are not MFA.

---

## Practice Questions — Day 8

Platform: examcompass.com
Questions: 20
Score: [your actual score] / 20

What I got wrong and the correct explanation:
[Write each wrong answer and why the right answer is correct]

---

## OverTheWire Bandit — Levels 8 and 9

### Level 7 to 8: Find the unique line in data.txt

Command used:
sort data.txt | uniq -u

How it works: sort arranges all lines alphabetically so duplicates are next to each other. uniq -u then prints only lines that appear exactly once — everything with a duplicate is filtered out.

Real-world use: Finding the one anomalous event among thousands of normal log entries.

### Level 8 to 9: Extract readable strings from a binary file

Command used:
strings data.txt | grep "==="

strings scans a binary file and extracts all sequences of printable characters. Used in malware analysis to find URLs, commands, and file paths inside binary executables.

---

## Commands Used Today

sort data.txt | uniq -u
strings data.txt | grep "==="
ssh bandit8@bandit.labs.overthewire.org -p 2220

---

## Tomorrow — Day 9

Security+ Domain 1 continues. Bandit levels 10 and 11 — base64 and ROT13. Python dictionaries and port scanner upgrade.
