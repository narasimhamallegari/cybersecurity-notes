# Day 12 — Zero Trust, Python File Handling, Log Analyzer Built

Date: April 14, 2026
Time spent: 2 hours

---

## Security+ Domain 3 — Security Architecture

### Zero Trust
Principle: never trust, always verify. No user or device is trusted by default, even inside the corporate network.

Why perimeter security failed: Remote work, cloud services, and mobile devices eliminated the concept of a clear network perimeter. Zero Trust treats every access request as if it comes from an untrusted network.

Components: identity verification, device health validation, least privilege access, micro-segmentation, and continuous monitoring.

### Defense in Depth
Multiple overlapping security layers. If one fails, others still protect. Like an onion — attacker must penetrate multiple independent layers.

### Network Segmentation
Dividing the network into isolated segments. Compromise in one segment does not automatically give access to everything else. Techniques: VLANs, subnetting, firewalls between segments.

### DMZ — Demilitarized Zone
Network segment between the internet and the internal network where public-facing servers live. If the DMZ server is compromised, the attacker still faces another firewall before reaching internal systems.

### Cloud Shared Responsibility Model

IaaS: Provider manages physical hardware. Customer manages OS, applications, and data.
PaaS: Provider manages hardware and OS. Customer manages applications and data.
SaaS: Provider manages everything except data and user access management.

---

## Python File Handling

Reading files:
with open("filename.txt", "r") as f:
    for line in f:
        process(line.strip())

Writing files:
with open("results.txt", "w") as f:
    f.write("content here\n")

Appending:
with open("log.txt", "a") as f:
    f.write("new entry\n")

Always use with open() — ensures file closes properly even if an error occurs.

---

## Log Analyzer — Third Python Security Tool

Reads Linux auth.log, finds failed SSH login attempts, counts per source IP, and ranks attackers.

from collections import Counter

failed_ips = []
with open(filename, "r") as f:
    for line in f:
        if "Failed password" in line:
            parts = line.split()
            for i, part in enumerate(parts):
                if part == "from":
                    failed_ips.append(parts[i + 1])

ip_counts = Counter(failed_ips)
for ip, count in ip_counts.most_common(10):
    print(ip + " — " + str(count) + " attempts")

Tested against /var/log/auth.log on the Kali machine.

Result: [write what you actually found — number of failed attempts, how many unique IPs]

This is what a SOC analyst does with SIEM at enterprise scale. My script does the same thing at the single log file level. Understanding the concept at the code level makes enterprise tools more intuitive.

Three tools now live in python-security-tools: port scanner, password generator, and log analyzer.

---

## Commands Used Today

sudo python3 log_analyzer.py
cat /var/log/auth.log | grep "Failed" | wc -l
