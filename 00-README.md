---

## **DVWA Penetration Testing Report**

--- 

**Tester:** Areebah Khan  
**Application:** DVWA v1.10 – Low Security  
**Date:** 07/09/25

---

**Project Overview**

This project explores common web application vulnerabilities in DVWA (Damn Vulnerable Web Application) Low Security environment. The main goal was to understand how attacks such as SQL Injection, Command Injection, File Inclusion, and Cross-Site Scripting (XSS) can compromise application security.

The project involves practical exploitation of these vulnerabilities in a controlled lab environment to observe the risks and impacts of unsafe coding practices.

---

**Objective**
- Identify and exploit DVWA Low Security vulnerabilities.
- Demonstrate real-world consequences of insecure input handling.
- Document exploitation steps, findings, and mitigation strategies.

---

**Scope**
- DVWA v1.10 Low Security Modules:
- SQL Injection
- Command Injection
- File Inclusion
- Reflected XSS
- Stored XSS
- DOM-Based XSS
- Testing performed locally in a Docker lab environment.

---

**Methodology**
1. Identify vulnerable input fields or parameters.
2. Craft safe payloads for demonstration purposes.
3. Exploit vulnerabilities to extract information, manipulate the application, or execute scripts.
4. Document observations, outputs, and impacts.
5. Recommend mitigations and best practices.

--- 

**Table of Contents**
| Vulnerability         | Objective                                              | Key Findings                                                                                         | Impact                                                        | Links to Files / Screenshots |
|-----------------------|--------------------------------------------------------|----------------------------------------------------------------------------------------------------|--------------------------------------------------------------|-------------------------------|
| SQL Injection (SQLi)  | Extract sensitive user data and enumerate database    | User input not sanitized → attacker can manipulate queries. <br> Able to retrieve DB version, DB name, table names, column names, and user credentials. | Full compromise of user accounts; sensitive data exposure    | (https://github.com/areebah890/attack_on_DVWA_using_burp/blob/main/01-SQLinjection.md)                    |
| Command Injection     | Execute arbitrary system commands via DVWA Ping module | Input not sanitized → commands executed on server. <br> Gathered system info: users, network interfaces, files. | Potential privilege escalation; system reconnaissance       | https://github.com/areebah890/attack_on_DVWA_using_burp/blob/main/02-Commandinjection.md)                   |
| File Inclusion (LFI)  | Read system files and extract DVWA database credentials | Unsanitized page parameter → could include /etc/passwd or config files. <br> Extracted DB credentials, logged in, and dumped all users. | Full database compromise, credential theft, application takeover | (https://github.com/areebah890/attack_on_DVWA_using_burp/blob/main/03-FileInclusion.md)                    |
| Reflected XSS         | Demonstrate unsanitized HTML/JS execution via input   | HTML input reflected back immediately. <br> JavaScript executed in victim’s browser.               | Session hijacking, phishing, or arbitrary browser actions    |                  (https://github.com/areebah890/attack_on_DVWA_using_burp/blob/main/04-ReflectedXSS.md)|
| Stored XSS            | Persistent script execution stored in DB              | Script stored in server-side DB; executed when other users view the page.                          | Persistent attack vector affecting all users; high risk for phishing or malware injection | https://github.com/areebah890/attack_on_DVWA_using_burp/blob/main/05-StoredXSS.md                    |
| DOM-Based XSS         | Exploit client-side JS to manipulate the DOM via URL | Input parameter `default` executed in browser. <br> Able to change page content, colors, and inject scripts. | Arbitrary DOM manipulation; browser-side security compromise | (https://github.com/areebah890/attack_on_DVWA_using_burp/blob/main/06-DOMXSS.md)                    |

--- 

**Key Findings**
- DVWA Low Security is highly vulnerable to multiple attack vectors.
- Unsanitized input and improper output handling are primary causes of vulnerabilities.
- Attacks can escalate from data disclosure to full system or application compromise.

---

**Lessons Learned**
- Proper input validation and output encoding are crucial.
- Understanding the difference between server-side (SQLi, LFI, Command Injection) and client-side (XSS) vulnerabilities is essential.
- Low-security lab environments are invaluable for safely practicing penetration testing techniques.
- Attack chaining is possible: one vulnerability can lead to further exploitation (e.g., LFI → database dump → credential theft).

---

**Recommendations/Mitigations**
1. Sanitize and validate all user inputs.
2. Use parameterized queries or prepared statements for database operations.
3. Escape or encode user output to prevent XSS.
4. Implement least privilege for server accounts.
5. Disable dangerous PHP functions (allow_url_include) and enforce open_basedir.
6. Enable Web Application Firewall (WAF) and monitor logs.
7. Use strong password hashing and storage techniques (salted hashes).


