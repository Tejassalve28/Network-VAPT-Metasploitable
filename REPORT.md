# Penetration Testing Engagement Report: Metasploitable2 Lab

##  1. Project Objective
The primary objective of this project was to conduct a comprehensive Network Vulnerability Assessment and Penetration Testing (VAPT) exercise against a target system (Metasploitable2) in an isolated lab environment.
The goal was to:
- Identify active network services and running daemons on the target machine.
- Discover legacy vulnerabilities and misconfigurations using automated scanning tools.
- Perform targeted service exploitation to gain unauthorized administrative (root) access.
- Demonstrate post-exploitation techniques including credential extraction and system enumeration.
- Provide actionable security recommendations to patch identified entry points.

---

##  2. Introduction
In modern network security, legacy software and unpatched services pose severe risks to organization infrastructure. Metasploitable2 is an intentionally vulnerable Linux virtual machine designed to simulate high-risk network environments.
This penetration testing report documents an end-to-end attack pipeline—from initial network discovery and port auditing to service exploitation (vsftpd backdoor and Samba usermap_script) and sensitive data extraction (/etc/shadow and /etc/passwd).
- **Attacker System:** Kali Linux (192.168.0.139)
- **Target System:** Metasploitable2 (192.168.0.113)
- **Environment:** Isolated VirtualBox Host-Only Network

---

##  3. Techniques & Skills Learned
Through this project, key practical cybersecurity concepts and hands-on offensive security techniques were mastered:
- **Network Reconnaissance:** Performing host discovery using ICMP sweep (ping) and service auditing using Nmap NSE scripts (`--script=vuln`).
- **Exploit Framework Utilization:** Searching, configuring, and executing remote exploits using the Metasploit Framework (`msfconsole`).
- **In-Memory Payload Execution:** Operating within interactive Meterpreter sessions to perform stealthy in-memory post-exploitation.
- **Privilege Verification:** Identifying user privileges (`getuid`) and system architecture details (`sysinfo`).
- **Credential Dumping & Enumeration:** Dropping into system shells to extract stored password hashes (`/etc/shadow`) and user account mapping (`/etc/passwd`).
- **Multi-Vector Exploitation:** Leveraging multiple distinct service vectors (FTP on port 21 and Samba/SMB on ports 139/445) to achieve complete system takeover.

---

##  4. Outcomes
- **Full Root Access Achieved:** Successfully gained root level privileges across two distinct service vectors (vsftpd backdoor and Samba usermap_script).
- **Sensitive File Extraction:** Successfully retrieved system password hashes (`/etc/shadow`), enabling potential offline password cracking attacks.
- **Attack Surface Mapped:** Documented all active open ports (21, 22, 23, 25, 139, 445) and identified weak/unencrypted protocols active on the target host.

---

##  5. Case Studies (Specific Exploits)

### Case Study A: vsftpd 2.3.4 Backdoor Exploitation
- **Vulnerability Type:** Malicious Source-Code Backdoor
- **Attack Vector:** Port 21 (FTP)
- **Exploration:** The running version of vsftpd contained a backdoored source code package that opens a listener shell on port 6200 when a specific string (`:)`) is supplied in the username.
- **Impact:** Executed `exploit/unix/ftp/vsftpd_234_backdoor` in Metasploit, spawning an immediate interactive Meterpreter session with full root control.

### Case Study B: Samba usermap_script Remote Command Execution
- **Vulnerability Type:** Command Injection / Input Validation Failure
- **Attack Vector:** Ports 139 / 445 (SMB)
- **Exploration:** Samba versions 3.0.20 through 3.0.25rc3 fail to sanitize user input when processing the username map script configuration option.
- **Impact:** Executed `exploit/multi/samba/usermap_script`, allowing arbitrary command injection as the root user and establishing a remote command shell.

---

##  6. Conclusion
This VAPT engagement demonstrated how unpatched legacy services allow attackers to breach a network and obtain highest-level privileges within minutes. Both the FTP backdoor and Samba command injection vulnerabilities enabled complete host compromise without requiring prior valid credentials.
Implementing proper patch management, disabling unnecessary legacy protocols, and enforcing network-level access control lists (ACLs) are essential defenses against these exploitation vectors.
