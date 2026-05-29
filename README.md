# Multi-Stage Corporate Network Penetration Testing Lab
 
A progressively hardened, 4-tier corporate network simulation built from physical hardware — designed, exploited, and evolved through 5 versions of increasing difficulty.
 
> Built on personally owned hardware. All IP addresses and credentials are sanitized.
 
---
 
## Project Overview
 
Most homelab projects use a single VM and a tutorial. This one simulates a real corporate network with segmented tiers, physical hardware, and a full attack chain that must be completed in sequence — just like a real penetration test.
 
The environment was built once, fully compromised, then hardened and compromised again — five times. Each version added new defensive controls that required new attack techniques to bypass.
 
---
 
## Network Architecture
 
```
[Kali Linux - Attacker]
         |
         v
[Layer 1] Windows 11 PC          — Employee Workstation
         |
         v  (firewall — only communicates with next tier)
[Layer 2] Raspberry Pi #1        — Jump Host / Bastion Host
         |
         v  (firewall — only communicates with next tier)
[Layer 3] Raspberry Pi #2        — Application Server
         |
         v  (firewall — only communicates with next tier)
[Layer 4] Raspberry Pi #3        — Production Database Server (Crown Jewel)
```
 
Each machine is firewalled to only communicate with the adjacent tier. An attacker must fully compromise each layer before moving to the next — no jumping ahead.
 
---
 
## Attack Chain
 
### Layer 1 — Windows 11 (Employee Workstation)
- Gain initial foothold as a low-privilege domain user
- Escalate to SYSTEM via local privilege escalation
- Locate and decrypt a DPAPI-protected SSH private key
- Use the decrypted key to pivot to Layer 2
### Layer 2 — Pi #1 (Jump Host / Bastion)
- SSH in using the key recovered from Layer 1
- Escalate to root via sudo misconfiguration
- Locate a Base64-encoded passphrase hidden on the system
- Decrypt the SSH key for Layer 3 and pivot forward
### Layer 3 — Pi #2 (Application Server)
- SSH in using credentials recovered from Layer 2
- Discover a command injection vulnerability in an internal web application
- Exploit command injection to obtain a reverse shell
- Escalate to root via a writable cron script
- Extract SSH key and passphrase for Layer 4
### Layer 4 — Pi #3 (Production Database Server)
- SSH in using credentials from Layer 3
- Land in a restricted shell (rbash) limited to 6 commands
- Identify a SUID binary with a writable dependent script
- Modify the script using only `echo` to inject a payload
- Execute the SUID binary to escalate to root
- Read the customer database — objective complete
---
 
## Version History — Progressive Hardening
 
| Version | Defensive Control Added | Difficulty |
|---------|------------------------|------------|
| V1 | Base environment — command injection, writable cron, SUID binary | Medium |
| V2 | 5-minute cron reset — cron script reverts every 5 minutes (time pressure) | Medium-Hard |
| V3 | Restricted shell (rbash) on Layer 4 — only 6 commands available | Hard |
| V4 | Login-triggered countdown timer — timer starts on SSH login, not on setup | Harder |
| V5 *(planned)* | Decoy files, read-only /tmp, AppArmor profiles, encrypted flags | Very Hard |
 
Each version required developing new techniques to bypass the added controls — the environment never stayed the same after being compromised.
 
---
 
## Technologies and Skills
 
### Operating Systems
- Windows 11
- Kali Linux
- Raspberry Pi OS (Debian)
### Networking
- Static IP configuration
- UFW firewall rules and tier isolation
- SSH tunneling and port forwarding
- Network segmentation design
### System Administration
- User and group management
- Linux file permissions (chmod, chown, SUID)
- Service configuration — sshd, Apache, cron, systemd
- Log analysis and troubleshooting
### Security Techniques
- Privilege escalation — Windows (DPAPI) and Linux (SUID, cron, sudo)
- Command injection exploitation
- Restricted shell escape (rbash)
- SSH key pivoting across network tiers
- Cron job abuse
- Reverse shell establishment
### Tools
- Nmap
- Netcat
- SSH
- Bash / PowerShell
- systemd / cron / UFW
---
 
## MITRE ATT&CK Coverage
 
| Technique | Name | Tactic | Layer |
|-----------|------|--------|-------|
| T1059 | Command and Scripting Interpreter | Execution | Layer 3 |
| T1548 | Abuse Elevation Control Mechanism | Privilege Escalation | Layer 1, 2, 4 |
| T1552 | Unsecured Credentials | Credential Access | Layer 1, 2, 3 |
| T1021.004 | Remote Services: SSH | Lateral Movement | All layers |
| T1055 | Process Injection | Privilege Escalation | Layer 4 |
| T1083 | File and Directory Discovery | Discovery | All layers |
| T1005 | Data from Local System | Collection | Layer 4 |
 
---
 
## What Makes This Different
 
**Physical hardware** — not VMs. Three Raspberry Pis and a Windows PC running as actual network hosts with real firewall rules between them.
 
**Progressive difficulty** — the environment was rebuilt and hardened after every successful compromise. V4 is significantly harder than V1 because real defensive controls were added, not just complexity.
 
**Full attack chain** — every layer must be compromised in sequence. There is no shortcut. This mirrors the structure of a real corporate network penetration test.
 
**Multi-platform** — Windows privilege escalation, Linux privilege escalation, and lateral movement across both — in the same engagement.
 
---
 
## Author
 
**Giovanni Moore**
[LinkedIn](https://www.linkedin.com/in/giovanni-moore-408589362/) | [GitHub](https://github.com/Truxnks)
 
