# Reflected Cross-Site Scripting (XSS) – DVWA Low Security

**Tester:** Areebah Khan  
**Application:** DVWA v1.10 – Low Security  
**Date:** 07/09/25

---

## Objective
Demonstrate Reflected Cross-Site Scripting (XSS) vulnerabilities and how unsanitized input can be executed in the browser, allowing an attacker to inject arbitrary JavaScript.

---

## Methodology
Testing was performed on the Reflected XSS (Low) page of DVWA. Input was submitted via the form field (`name`) and verified through URL parameters. All payloads were crafted to illustrate XSS without affecting other users or data in the DVWA lab environment.

Steps followed:  
1. Confirm XSS vulnerability with inline input.  
2. Execute a JavaScript alert via URL-based injection.  
3. Observe reflected payload execution in the browser.  

---

## Exploitation Steps

<details> <summary>Step 1: Confirm XSS Vulnerability</summary>

**Payload:** `<b>test</b>`  

**Observation:**
- Text “test” appeared in bold on the page.  
- Confirms the page does not sanitize HTML input.  

**Impact:**
- Unsanitized input allows arbitrary HTML and JavaScript injection.
- Attackers could inject scripts to steal cookies, hijack sessions, or perform actions as users.

<img width="940" height="519" alt="image" src="https://github.com/user-attachments/assets/8117618c-b1b2-4634-86ae-75b642ec3d1f" />


</details>

<details> <summary>Step 2: Inject JavaScript Alert</summary>

**Payload:** `<script>alert('XSS');</script>`  

**Observation:**
- A JavaScript alert popped up immediately in the browser.  
- This confirms the input is executed, demonstrating Reflected XSS.

**Impact:**
- Attacker can execute arbitrary scripts in the context of the victim’s browser.
- Could be used for phishing, stealing session cookies, or redirecting users.

<img width="940" height="786" alt="image" src="https://github.com/user-attachments/assets/ca4817d1-acff-418c-b5db-53e8efe00160" />


</details>

<details> <summary>Step 3: URL-Based Injection</summary>

**URL Example:**  http://localhost/vulnerabilities/xss_r/?name=<script>alert('XSS')</script>

**Observation:**
- Payload is reflected immediately in the server response.
- Does not get stored; this is Reflected XSS.
- The next step could be Stored XSS, which persists in the database.

**Impact:**
- URL parameters can be manipulated to execute scripts on user browsers.
- Could be used for targeted attacks via crafted links (phishing or session theft).

<img width="940" height="734" alt="image" src="https://github.com/user-attachments/assets/14441f33-4dae-4bcd-8952-31a550ae1782" />

</details>

---

## Summary
- DVWA Low Security is vulnerable to Reflected XSS.  
- Exploitation path:
  1. Submit unsanitized HTML input.  
  2. Inject JavaScript via form field or URL parameter.  
  3. Observe script execution in browser.  
- Real-world implications: session hijacking, phishing, and arbitrary browser-side actions.

---

## Recommendations/Mitigations
1. Sanitize all user inputs using libraries or frameworks (e.g., output encoding).  
2. Implement Content Security Policy (CSP) headers.  
3. Escape special characters in HTML contexts.  
4. Validate and whitelist input where possible.  
5. Educate developers on XSS attack vectors and testing.

---

## XSS Flow Diagram

```mermaid
graph TD
A[User Input] --> B[DVWA Reflected XSS]
B --> C{Sanitized?}
C -- No --> D[XSS Success: Script Executes in Browser]
C -- Yes --> E[Safe Output / Escaped]
