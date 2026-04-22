# Day 18 — Security+ Domain 5: Risk Management, Compliance Frameworks


---

## Risk Management

Risk = Threat × Vulnerability × Impact. Zero in any factor = zero risk.

### Four Risk Response Strategies

Accept: Tolerate the risk. Cost of mitigation exceeds cost of incident.
Transfer: Insurance or contracts move financial responsibility.
Avoid: Stop the risky activity entirely — eliminates the risk.
Mitigate: Implement controls to reduce the likelihood or impact. Most common response.

---

## Five Compliance Frameworks

### HIPAA
Protects patient health information (PHI). Healthcare industry. Breach notification within 60 days. Penalty up to $1.9M per year.

### PCI-DSS
Protects payment card data. Any org that processes credit cards. Requires encryption, access control, vulnerability scanning, and annual penetration test. Contractual, not legal.

### GDPR
Protects EU citizens' personal data worldwide. Breach notification within 72 hours. Right to erasure. Penalty up to 4% of global annual revenue.

### SOC 2
Voluntary audit for service providers. Five Trust Service Criteria: Security, Availability, Processing Integrity, Confidentiality, Privacy.

### NIST CSF
Five functions: Identify → Protect → Detect → Respond → Recover. Used by the US government. Widely adopted by the private sector voluntarily.

---

## Splunk Modules 3 and 4

Field transformations and dashboard creation.

Search showing failed logins per hour:
index=main "Failed password" | timechart count span=1h

---

## Bandit Levels 22 and 23

Analyzed cron scripts using md5sum file naming to predict output filenames. Then wrote shell script placed in writable cron input directory for privileged execution. Cron job exploitation through writable directories is a real privilege escalation technique.
