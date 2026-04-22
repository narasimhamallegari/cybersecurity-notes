# Day 15 — Security+ Domain 4 Begins + Incident Response + First Job Applications

---

## Security+ Domain 4 — Incident Response

The 6 phases in exact order (Security+ tests the order strictly):

1. Preparation: Policies, tools, training in place BEFORE an incident. An untested plan is not real preparation.

2. Identification: Determine whether an incident is actually occurring. SIEM alerts, user reports, anomaly detection. Distinguish real incidents from false positives.

3. Containment: Stop the spread. Short-term: isolate affected systems immediately. Long-term: patch, clean, rebuild.

4. Eradication: Remove the threat entirely. Delete malware, close access vectors, remove attacker tools, and backdoors. Containment without eradication means the attacker is still inside.

5. Recovery: Restore systems to normal operation. Verify clean before reconnecting. Monitor closely for 30-90 days for reinfection.

6. Lessons Learned: Document what happened, what detection failed, what containment was slow, and what training gap was exposed. Update the plan. Train on gaps. Most skipped, most valuable long-term.

### Threat Intelligence Types

Strategic: High-level trends for executives — "ransomware targeting healthcare increased 300%."
Tactical: Specific TTPs used by threat actors
Operational: Information about specific upcoming attacks
Technical: Actionable indicators — IP addresses, domains, file hashes

---

## First Job Applications Sent Today

Applied to 5 roles

Searched terms used: SOC Analyst entry level, Security Analyst junior remote

Target H1B sponsors: Deloitte, IBM, Accenture, Booz Allen Hamilton, Leidos, SAIC

---

## Bandit Levels 16 and 17

Level 15 to 16: Combined Nmap version scanning with SSL to find which port in a range speaks SSL.
nmap -sV -p 31000-32000 localhost
echo "password" | openssl s_client -connect localhost: PORT -quiet

Level 16 to 17: Used diff to find the changed line between two files.
diff passwords. old passwords.new
Used in forensics to detect unauthorized file modifications and verify configuration changes.
