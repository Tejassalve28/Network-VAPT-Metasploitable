# Network-VAPT-Metasploitable
Hands-on vulnerability assessment and penetration testing lab on Metasploitable 2 using Kali Linux and Metasploit.

## Initial Reconnaissance and Vulnerability Scanning
- Nmap (Network Mapper) is an open-source tool used by security professionals to scan networks,
- discover active devices, find open ports, and identify running services along with their security
  flaws.
-  In a VAPT workflow, Nmap acts as the primary tool during the reconnaissance phase to build a
  map of the target's attack surface

# Command Used in This Step
-sudo nmap --script=vuln 192.168.0.113
<img width="1920" height="950" alt="image" src="https://github.com/user-attachments/assets/7416f651-3bdd-4e8c-ba7b-cafa7645e3d1" />
# What this command does
- sudo: Runs the command with administrative privileges, which allows Nmap to craft raw network packets for accurate scanning.
- nmap: Invokes the scanner tool
- script=vuln: Triggers Nmap’s built-in Scripting Engine (NSE) vulnerability categories to check the open ports against known security vulnerabilities (CVEs).
- 192.168.0.113: The target IP address (Metasploitable instance) being assessed.
  
### Target Selection Based on Scan Results
- Based on the initial Nmap scan, two specific services were selected for exploitation due to their known critical vulnerabilities:
- 1. FTP (`vsftpd` on Port 21):** The scan revealed `vsftpd 2.3.4` running on port 21, which contains a well known built in backdoor allowing instant root-level shell access.
- 2. Samba (SMB on Ports 139/445):** The active Samba service was targeted due to a command injection vulnerability (`usermap_script`), enabling remote code execution (RCE) with administrative privileges.
- These two entry points were prioritized to demonstrate service-level exploitation and privilege.



### Phase 2: Exploitation - vsftpd 2.3.4 Backdoor

### Step 1 Launching Metasploit and Searching for the Exploit Module

- After identifying the open FTP service during the scanning phase, I opened a new terminal and launched the Metasploit Framework console (`msfconsole`). 
- I then searched for available exploit modules targeting the `vsftpd` service using the following command.
-bash
- msfconsole
- msf > search vsftp
<img width="1920" height="950" alt="Screenshot_2026-10-17_12_01_18" src="https://github.com/user-attachments/assets/f10b3e83-e9a8-4f7e-bff0-4313695ce6b1" />
# Observations & Output:
# The search query returned two matching modules:   
- 0 auxiliary/dos/ftp/vsftpd_232 (Denial of Service)
- 1 exploit/unix/ftp/vsftpd_234_backdoor (VSFTPD 2.3.4 Backdoor Command Execution)
- msf > use 1
  
