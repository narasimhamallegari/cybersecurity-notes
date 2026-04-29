# Day 22 — Domain 1 and 2 Full Review + TryHackMe SOC Level 1 Starts

Date: April 29, 2026
Time spent: 2.5 hours
Week 4 begins: shift from learning to reviewing

---

## How this week is different

All 5 Security+ domains have been covered. Week 4 is not about new content. It is about finding every remaining gap and closing it before June 9. The exam is 5 weeks away.

Today I tested myself on every Domain 1 and 2 concept before watching any review videos. Every concept I could not explain confidently got a full Messer video review. This is more effective than rewatching everything — targeted review saves time and focuses energy where it is actually needed.

---

## Domain 1 Self-Test Results

CIA Triad — explained all 3 without notes: YES / NO
Authentication vs Authorization — could explain the distinction: YES / NO
5 security control types — named and gave example of each: YES / NO
Symmetric vs asymmetric — explained difference, named examples: YES / NO
Hashing — explained one-way, gave MD5 vs SHA-256 distinction: YES / NO

---

## Domain 1 Key Concepts — Condensed Review

CIA Triad:
Confidentiality — only authorized access. Breach = confidentiality failure.
Integrity — data unmodified. MITM that alters data = integrity failure.
Availability — accessible when needed. DDoS = availability failure.

Authentication vs Authorization:
Authentication = proving identity (who you are)
Authorization = determining access rights (what you can do)
Both must work correctly. Broken access control is in the OWASP Top 10 every year.

Security Controls:
Preventive — stops attacks: firewall, encryption, access control
Detective — identifies attacks: SIEM alert, IDS, camera
Corrective — fixes damage: backup restore, patch
Deterrent — discourages: visible cameras, warning signs
Compensating — alternative when primary impossible: monitoring instead of patch

Cryptography:
Symmetric: the same key encrypts and decrypts. Fast. Key distribution problem. Examples: AES, 3DES.
Asymmetric: public key encrypts, private key decrypts. Solves key distribution. Slower. Examples: RSA, ECC.
Hashing: one-way. Cannot be reversed. MD5 (broken — 10B/sec crackable), SHA-256 (current standard).
HTTPS: asymmetric for key exchange → symmetric for data (speed).

---

## Domain 2 Self-Test Results

All 8 malware types — named and distinguished: YES / NO
Virus vs worm distinction: YES / NO
All 8 social engineering types: YES / NO
Pretexting vs phishing distinction: YES / NO

---

## Domain 2 Key Concepts — Condensed Review

Malware types and key distinctions:
Virus: needs human action to spread. Attaches to files.
Worm: spreads automatically. Exploits vulnerabilities. WannaCry = worm via SMB port 445.
Trojan: disguised as legitimate software. User installs voluntarily.
Ransomware: encrypts files, demands payment. Defense = offline backups.
Spyware: silent surveillance. Transmits keystrokes and credentials.
Rootkit: hides from the OS. Operates below the OS level.
Keylogger: records keystrokes. Software or hardware.
Botnet: a network of infected machines. C2 communication.

Social engineering attacks:
Phishing: mass email, fake legitimate org
Spear phishing: targeted, uses personal details
Whaling: targets executives specifically
Vishing: voice call, fake IT/bank/government
Smishing: SMS with a malicious link
Pretexting: a fabricated scenario to manipulate
Baiting: infected USB in the parking lot
Tailgating: following an authorized person through a secure door

---

## Practice Questions — Day 22

Platform: examcompass.com
Focus: Domain 1 and 2 topics
Questions: 40
Score: 37 / 40

---

## TryHackMe SOC Level 1 — Started

Room completed today: Cyber Threat Intelligence
Link: tryhackme.com/room/cyberthreatintel
Status: Complete
