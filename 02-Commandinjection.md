
---

## **Command Injection – DVWA Low Security**

**Tester:** Areebah Khan  
**Application:** DVWA v1.10 – Low Security  
**Date:** 07/09/25

## Objective
Demonstrate command injection vulnerabilities and exploitation steps to extract sensitive information and system details.

---

## Methodology
Testing was performed on the Command Injection (Low) page of DVWA. Input was submitted via the Ping form field, which executes a system ping command on the server. Payloads were crafted to safely test command execution and confirm system exposure.

Steps followed:

1. Confirm vulnerability.
2. Execute system commands.
3. Gather system/network information.
4. Retrieve sensitive files.
5. Identify high-value targets.

---

## Exploitation Steps
<details> <summary>Step 1: Confirm Vulnerability</summary>


**Payload:** 127.0.0.1; echo TEST

**URL Encoded:** 127.0.0.1%3B+echo+TEST

**Observation:**
- Normal ping output displayed.
- TEST printed in response → confirms server executed second command.

**Impact:**
- Confirms unsanitized input is directly passed to system shell.
- Attackers can append arbitrary commands after ;.

<img width="940" height="632" alt="image" src="https://github.com/user-attachments/assets/31c1fd54-be50-439b-bea9-82ea387397af" />


</details>


<details> <summary>Step 2: List Files in Web Directory</summary>

**Payload:** 127.0.0.1; ls

**URL Encoded:** 

**Observation:**
- Returned files in DVWA’s web root:
help
index.php
source

**Impact:**
- Confirms directory traversal possible.
- Attackers can enumerate files and scripts hosted on the server.

<img width="940" height="640" alt="image" src="https://github.com/user-attachments/assets/db8d2bb7-2a0d-41e6-a06a-866bf2e7f530" />


</details>

<details> <summary>Step 3: Identify Current User</summary>

**Payload:** 127.0.0.1; whoami

**URL Encoded:** 127.0.0.1%3B+whoami

**Observation:** 
- Output → www-data
- This is the default web server user for Apache in Debian-based systems.
- This confirms command injection succeeded and reveals the user the web server is running as.

**Impact:** 
- Identifies privileges of the web server user, helping plan further attacks.

<img width="940" height="657" alt="image" src="https://github.com/user-attachments/assets/07e5fd6a-7b38-47c8-856a-896b53a27221" />

</details>

<details> <summary>Step 4: Gather Network Information</summary>

**Payload:** 127.0.0.1; ip a

**URL Encoded:** 127.0.0.1%3B+ip+a

**Observation:**
- Loopback interface: 127.0.0.1
- Ethernet interface: 172.17.0.2
- This shows both IPv4 and IPv6 addresses assigned to the server.

**Impact:**

- Leaks network topology and internal IP addresses.
- This info can be used to map the network or find other targets.

<img width="940" height="646" alt="image" src="https://github.com/user-attachments/assets/f8dfcaa3-2093-4e4b-886e-3eaa66b608e2" />


</details>

<details> <summary>Step 5: Read Sensitive Files</summary>

**Payload:** 127.0.0.1; cat /etc/passwd

**URL Encoded** 127.0.0.1%3B+cat+/etc/passwd

**Observation:** 
- Retrieved full list of system users:
- High-value: root, www-data, mysql
- Service accounts: daemon, bin, sys, etc.

**Impact:**

- Attackers can see all system user accounts, which could help in further attacks (privilege escalation, brute-forcing passwords, targeting specific services).


<img width="940" height="689" alt="image" src="https://github.com/user-attachments/assets/3912ccd9-004e-4964-a799-a93923a648b8" />

**High-Value Targets**
- root → Full administrative privileges (UID 0).
- www-data → Already compromised; attacker can escalate from here.
- mysql → Potential database access and sensitive file dumps.

**Medium-Value Targets:**
- Service accounts (daemon, bin, sys, etc.) with no login shell.

**Low-Value Targets:**
- Accounts with no shell or files exposed.
</details>

---

**Summary:**
- DVWA Low Security is vulnerable to command injection.
- Successful exploitation allows:

Arbitrary command execution as www-data.
Disclosure of internal network details.
Access to sensitive system files (/etc/passwd).

- Real-world implications:
Attackers could escalate privileges or pivot further into the environment.

---

**Recommendations/Mitigations:**
1. Sanitize and validate user input — disallow shell metacharacters (;, &&, |).
2. Use parameterized system calls (e.g., execve() with arguments).
3. Run web applications with least privileges.
4. Monitor logs for suspicious command patterns.
5. Use WAF or IDS/IPS to block command injection attempts.

--- 

<img width="1189" height="131" alt="command_injection_flow" src="https://github.com/user-attachments/assets/a08a6f0c-c923-4b3d-af8c-8873aecdb9c0" />


