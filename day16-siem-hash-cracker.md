# Day 16 — SIEM, Vulnerability Management, Hash Cracker Built

---

## Security+ Domain 4 — SIEM

SIEM collects and correlates logs from across the organization in real time. Generates alerts when patterns match attack signatures or anomalous behavior.

Most used: Splunk, Microsoft Sentinel, IBM QRadar, Elastic SIEM.

SOC analyst daily SIEM work: monitor dashboards, investigate alerts, correlate events to build attack timelines, escalate confirmed incidents, and document findings.

### Vulnerability Scanning vs Penetration Testing

Vulnerability scan: Automated. Identifies known vulnerabilities. Does NOT exploit them. Non-intrusive. Tools: Nessus, OpenVAS, Qualys.

Penetration test: Human tester actively exploits vulnerabilities. Test the real impact. Authorized and scoped. Produces a detailed report with risk ratings and remediation steps.

Key distinction on Security+ exam: scanning identifies, penetration testing exploits.

### CVSS Score

0–10 scale. Critical 9-10 (patch in 24 hours), High 7-8.9 (72 hours), Medium 4-6.9 (next maintenance window), Low 0.1-3.9 (regular cycle).

---

## Hash Cracker — Fourth Python Security Tool

Tests MD5, SHA1, and SHA256 hashes against wordlist. Tested against the MD5 hash of "password123" using rockyou.txt (14 million real leaked passwords).

Result: found in [your actual time].

Why this demonstrates: MD5 on consumer hardware = approximately 10 billion attempts per second. bcrypt at cost factor 12 = approximately 100 attempts per second. That is 100 million times slower — making dictionary attacks computationally impractical against properly hashed passwords.

Uploaded to python-security-tools. Four tools total.

---

## Bandit Levels 18 and 19

Level 17 to 18: Bypassed modified .bashrc by running commands non-interactively through SSH.
ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"
Attackers modify startup files for persistence. This technique bypasses that restriction.

Level 18 to 19: SUID binary exploitation.
./bandit20-do cat /etc/bandit_pass/bandit20
SUID binaries run as their owner. If owned by root, executing them grants root-level operations for that action.
