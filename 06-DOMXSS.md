# DOM-Based Cross-Site Scripting (XSS) – DVWA Low Securityy

**Tester:** Areebah Khan  
**Application:** DVWA v1.10 – Low Security  
**Date:** 07/09/25

---

## Objective

Demonstrate DOM-based Cross-Site Scripting (XSS) vulnerabilities, where unsanitized user input in the URL is directly handled by JavaScript in the browser, allowing arbitrary script execution.

---

## Methodology
Testing was performed on the DOM XSS (Low) page of DVWA. Input was submitted via the URL parameter default, which is processed by client-side JavaScript to display content. Payloads were crafted to demonstrate DOM XSS in a controlled environment without affecting other users.

Steps followed:
1. Confirm DOM XSS vulnerability with a basic alert.
2. Inject JavaScript to manipulate page appearance.
3. Test multiple payloads to illustrate arbitrary DOM manipulation.

---

## Exploitation Steps

<details> <summary>Step 1: Confirm DOM XSS</summary>

**URL Payload:** http://localhost/vulnerabilities/xss_d/?default=<script>alert(1)</script>

**Observation:**
- Browser executed JavaScript alert immediately.
- Confirms the page takes user input from the URL and directly executes it in the DOM.

**Impact:**
- Unsanitized client-side input allows arbitrary script execution.
- Could be used to hijack user sessions or manipulate page content.

<img width="940" height="757" alt="image" src="https://github.com/user-attachments/assets/3f009a54-bf76-4c27-b1e8-b96f072983c0" />


</details>

<details> <summary>Step 2: Change Background Color</summary>

**URL Payload:** http://localhost/vulnerabilities/xss_d/?default=<script>document.body.style.backgroundColor='yellow';</script>

**Observation:**
- Background of the page changed to yellow.
- Demonstrates control over DOM via JavaScript injection.

**Impact:**
- Attacker can manipulate appearance or content of the page for all visitors who view the URL.

<img width="940" height="966" alt="image" src="https://github.com/user-attachments/assets/8d23d932-f0da-49e9-883e-8d8bb4459469" />


</details>

<details> <summary>Step 3: Change Text Color</summary>

URL Payload: http://localhost/vulnerabilities/xss_d/?default=<script>document.getElementById('main_body').style.color='red';</script>

Observation:

Text color of the main body changed to red.

Confirms DOM manipulation is possible via URL parameter injection.

Impact:

Attackers can visually alter the page to mislead users or inject malicious content.

<img width="940" height="623" alt="image" src="https://github.com/user-attachments/assets/domxss_text_red.png" /> </details> <details> <summary>Step 4: Replace Page Content</summary>

**URL Payload:** http://localhost/vulnerabilities/xss_d/?default=<script>document.body.innerHTML='XSS DOM ;)!';</script>

**Observation:**
- Entire body replaced with injected message.
- Confirms full DOM content can be overwritten via input parameter.

**Impact:**
- Attacker can inject fake content, phishing messages, or misleading instructions.

<img width="940" height="522" alt="image" src="https://github.com/user-attachments/assets/5a216abe-98ec-4621-b8bb-6d8d0c01a3c8" />


</details>

<details> <summary>Step 5: Combined Payload (Background + Content)</summary>

**URL Payload:** http://localhost/vulnerabilities/xss_d/?default=<script>document.body.style.backgroundColor='yellow';document.body.innerHTML='<h1 style="color:red;">DOM XSS ;)!</h1>';</script>

**Observation:**
- Background changed to yellow, text replaced with a red heading.
- Demonstrates complete DOM control via a single payload.

**Impact:**
- High risk: attacker can combine multiple actions to mislead users, steal session data, or execute further scripts.

<img width="940" height="354" alt="image" src="https://github.com/user-attachments/assets/df986652-d0ce-4def-af35-4ed1d8990b01" />


</details>

---

**Summary:**
- DVWA Low Security is vulnerable to DOM-Based XSS.
- Exploitation path:
Submit unsanitized input in the default URL parameter.
JavaScript on the page directly executes the input.
Payloads can manipulate style, content, or execute arbitrary scripts.
- Real-world implications: page defacement, phishing, session hijacking, or arbitrary DOM manipulation.

---

**Recommendations/Mitigations:**
1. Sanitize all client-side input before processing in JavaScript.
2. Use textContent or safe APIs instead of innerHTML for dynamic content.
3. Validate and whitelist allowed input values.
4. Implement Content Security Policy (CSP) headers.
5. Educate developers on DOM XSS risks and secure coding practices.

---
```mermaid
graph TD
A[User Input] --> B[DVWA DOM XSS]
B --> C{Sanitized?}
C -- No --> D[XSS Success: Script Executes in Browser DOM]
C -- Yes --> E[Safe Output / Escaped Content]
'''

