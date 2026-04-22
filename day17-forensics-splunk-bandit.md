# Day 17 — Digital Forensics, Threat Hunting, Splunk Started


---

## Security+ Domain 4 — Digital Forensics

### Order of Volatility (collect the most volatile first)

1. CPU registers and cache — lost immediately on power off
2. RAM — running processes, network connections, encryption keys
3. Swap file
4. Hard drive contents
5. Log files
6. Archived media

If a forensic investigator powers off a machine before collecting RAM, evidence that exists only in memory is permanently gone. Some malware exists only in RAM to exploit this.

### Chain of Custody

Documentation of who collected evidence, when, how stored, and who had access. Required for legal proceedings. Break the chain = evidence inadmissible.

### IOCs (Indicators of Compromise)

Evidence of breach: unusual outbound connections, new scheduled tasks, modified system files, new user accounts, lateral movement between hosts, unusual processes, large data transfers to external destinations.

### TTPs and MITRE ATT&CK

TTPs: Tactics (what the attacker wants), Techniques (how they achieve it), Procedures (specific implementation).

MITRE ATT&CK: Free knowledge base mapping attacker TTPs across the full attack lifecycle. Used by defenders to build detection rules and by red teamers to simulate realistic attacks.

---

## Splunk Fundamentals 1 — Started

Platform: splunk.com/training (free course)
Modules completed: Introduction to Splunk, Basic Searching

Key SPL command:
index=main "Failed password" | stats count by src_ip | sort -count | head 10

This is identical to my Python log analyzer operation — same logic, enterprise SIEM syntax.

---

## Bandit Levels 20 and 21

Level 19 to 20: Netcat client-server communication — set up a listener, connected a SUID binary to it.
echo "password" | nc -l -p 1234 &
./suconnect 1234

Level 20 to 21: Cron job investigation.
cat /etc/cron.d/cronjob_bandit22
Cron jobs for persistence — attackers add malicious cron jobs to run malware after reboot.
