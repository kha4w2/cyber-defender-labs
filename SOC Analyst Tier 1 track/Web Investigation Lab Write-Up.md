# Web Investigation Lab Write-Up: Network Traffic Analysis & Web Server Compromise

## **Lab Overview**
- **Platform**: CyberDefenders
- **Lab Name**: Web Investigation
- **Category**: Network Forensics
- **Tactics**: Initial Access, Persistence, Command and Control
- **Tools Used**: Wireshark
- **Difficulty**: Easy
- **Scenario**: Examine network traffic with Wireshark to investigate a web server compromise, identify SQL injection, extract attacker credentials, and detect uploaded malware.

---

## **Investigation Steps & Answers**

### **Q1: Attacker’s IP Address**
**Question:** By knowing the attacker's IP, we can analyze all logs and actions related to that IP and determine the extent of the attack, the duration of the attack, and the techniques used. Can you provide the attacker's IP?

**Method:**
- Open the PCAP in Wireshark.
- Apply a filter for HTTP traffic: `http`.
- Navigate to **Statistics → Conversations** (IPv4 tab) to identify IP pairs with high traffic volume.

**Observation:**
- Two IPs show significant conversation: `111.224.250.131` and `73.124.22.98`.
- The traffic from `111.224.250.131` exceeds 28 MB – unusually high for a web server visitor.
- To confirm, filter HTTP requests from this IP: `ip.src == 111.224.250.131 && http`.
- Examine User‑Agent strings; they reveal usage of penetration testing tools like `sqlmap` and `gobuster`.

**Answer:** `111.224.250.131`

<img width="975" height="218" alt="image" src="https://github.com/user-attachments/assets/a01dc596-c706-46ad-8118-6c1fe7d56c87" />

> *Figure 1: IPv4 Conversations highlighting 111.224.250.131.*

<img width="975" height="523" alt="image" src="https://github.com/user-attachments/assets/e028f7c1-08a6-4ffa-9c46-11749cc2153e" />

> *Figure 2: HTTP requests showing sqlmap and gobuster User-Agents.*

---

### **Q2: Attacker’s City of Origin**
**Question:** If the geographical origin of an IP address is known to be from a region that has no business or expected traffic with our network, this can be an indicator of a targeted attack. Can you determine the origin city of the attacker?

**Method:**
- Use a GeoIP lookup service (e.g., AbuseIPDB) for IP `111.224.250.131`.

**Finding:**
- The IP originates from **Shijiazhuang, China**.

**Answer:** `Shijiazhuang`

<img width="975" height="475" alt="image" src="https://github.com/user-attachments/assets/a42b0b6a-68a3-4f0d-9caa-9d6a9a7bd20c" />

> *Figure 3: AbuseIPDB lookup showing city Shijiazhuang.*

---

### **Q3: Vulnerable PHP Script Name**
**Question:** Identifying the exploited script allows security teams to understand exactly which vulnerability was used in the attack. This knowledge is critical for finding the appropriate patch or workaround to close the security gap and prevent future exploitation. Can you provide the vulnerable PHP script name?

**Method:**
- Filter HTTP requests from the attacker: `ip.src == 111.224.250.131 && http`.
- Scan the **Info** column for script names being accessed.

**Observation:**
- Repeated requests to **`search.php`** with various parameters.

**Answer:** `search.php`

<img width="975" height="512" alt="image" src="https://github.com/user-attachments/assets/64266a5c-9a68-41a5-a7db-ac55e72f4573" />

> *Figure 4: HTTP requests showing search.php in the URI.*

---

### **Q4: First SQL Injection Attempt (Full Request URI)**
**Question:** Establishing the timeline of an attack, starting from the initial exploitation attempt, what is the complete request URI of the first SQLi attempt by the attacker?  
*Note: Decode the Value.*

**Method:**
- Filter traffic from attacker to victim web server:  
  `http && ip.dst == 73.124.22.98 && ip.src == 111.224.250.131`
- The question mentions SQL injection; a common test pattern is `1=1`.
- Use **Find Packet** (Ctrl+F) and search for the string `1=1`.

<img width="975" height="507" alt="image" src="https://github.com/user-attachments/assets/33c51e64-cdf8-4993-8f22-ddf87e0fc43c" />


**Result:**
- Locate the first packet containing `1=1`.
- Follow the **HTTP Stream** to view the full request.

**Decoding:**
- The URI is URL‑encoded: `/search.php?search=book%20and%201=1;%20\--%20-`
- Decode to get:  
  `/search.php?search=book and 1=1; -- -`

**Answer:** `/search.php?search=book and 1=1; -- -`

<img width="975" height="532" alt="image" src="https://github.com/user-attachments/assets/724f00b2-7f45-4781-8061-c4827c6801d7" />

> *Figure 5: HTTP stream showing the encoded request.*

<img width="975" height="1013" alt="image" src="https://github.com/user-attachments/assets/69c82f0d-0cda-4286-9d04-edc6853ca622" />

> *Figure 6: Decoded request URI.*

---

### **Q5: Request URI Used to Read Available Databases**
**Question:** Can you provide the complete request URI that was used to read the web server's available databases?  
*Note: Decode the Value.*

**Method:**
- Filter HTTP requests from attacker: `ip.addr == 111.224.250.131 && http.request`.
- Use **Find Packet** to search for strings related to database enumeration, e.g., `information_schema.schemata`.
- Locate the packet and follow the HTTP stream.

**Result:**
- The request URI contains a complex UNION query with hex‑encoded values.
- After URL decoding, the full URI is:

```
/search.php?search=book' UNION ALL SELECT NULL,CONCAT(0x7178766271,JSON_ARRAYAGG(CONCAT_WS(0x7a76676a636b,schema_name)),0x7176706a71) FROM INFORMATION_SCHEMA.SCHEMATA-- -
```

**Answer:** (as above)

<img width="975" height="471" alt="image" src="https://github.com/user-attachments/assets/c1ed6a29-5fab-409d-9538-69f2988f3831" />

> *Figure 7: Packet containing information_schema.schemata.*

<img width="975" height="336" alt="image" src="https://github.com/user-attachments/assets/249bff58-5fba-4c22-b10d-a7e73c26c5ce" />

> *Figure 8: HTTP stream showing the full query.*

<img width="975" height="472" alt="image" src="https://github.com/user-attachments/assets/b5ba812f-863c-40c0-8b16-312f0900820e" />


> *Figure 9: Decoded URI.*

---

### **Q6: Table Name Containing Website Users Data**
**Question:** Assessing the impact of the breach and data access is crucial, including the potential harm to the organization's reputation. What's the table name containing the website users data?

**Method:**
- Continue examining SQL injection attempts. Attackers often query `information_schema.tables` to find user‑related tables.
- Filter `ip.addr == 111.224.250.131 && http.request` and search for `information_schema.tables`.
- Follow the HTTP stream to see the query.

**Observation:**
- The query retrieves table names, and among them is **`customers`** – a likely table for user data.

**Answer:** `customers`


<img width="975" height="520" alt="image" src="https://github.com/user-attachments/assets/364d594d-45ff-4f37-b66e-ad5a8ee12587" />


> *Figure 10: Packet with information_schema.tables query.*



> *Figure 11: HTTP stream revealing table names.*

---

### **Q7: Directory Discovered by Attacker**
**Question:** The website directories hidden from the public could serve as an unauthorized access point or contain sensitive functionalities not intended for public access. Can you provide the name of the directory discovered by the attacker?

**Method:**
- The attacker used directory brute‑forcing tools. Filter HTTP requests from attacker with gobuster User‑Agent:
  ```
  http.request && ip.src == 111.224.250.131 && http.user_agent contains "gobuster"
  ```
- Examine the requested URIs.

**Observation:**
- Many requests target the **`/admin/`** path, which returned valid responses (e.g., HTTP 200).

**Answer:** `/admin/`



> *Figure 12: Gobuster requests showing /admin/ directory.*

---

### **Q8: Credentials Used for Login**
**Question:** Knowing which credentials were used allows us to determine the extent of account compromise. What are the credentials used by the attacker for logging in?

**Method:**
- After discovering the admin directory, look for login attempts (POST requests to `/admin/` or similar).
- Filter HTTP POST requests from attacker:  
  `ip.src == 111.224.250.131 && http.request.method == "POST"`
- Examine each POST packet; look for `Authorization` headers or form data.

**Observation:**
- Several POST attempts to `/admin/index.php` (or similar). One of them returns a successful login (not "invalid").
- The credentials appear in the packet details, possibly Base64‑encoded.

**Extraction:**
- Locate the successful login packet; find the line containing `username=admin&password=admin123!` or equivalent.
- Decode if necessary.

**Answer:** `admin:admin123!`

<img width="975" height="132" alt="image" src="https://github.com/user-attachments/assets/6df04d54-0091-4f08-b07f-6f122f52f892" />

> *Figure 13: POST request with login credentials.*

<img width="975" height="522" alt="image" src="https://github.com/user-attachments/assets/88287038-c142-457b-b55d-fe78ce0fcd2b" />

<img width="975" height="522" alt="image" src="https://github.com/user-attachments/assets/97395ecb-b9d6-4add-a615-6231dbda4ccb" />

<img width="975" height="811" alt="image" src="https://github.com/user-attachments/assets/3a524d30-f4d7-429d-b5fb-7ecc5e4e3072" />


> *Figure 14: Successful login response.*

<img width="975" height="870" alt="image" src="https://github.com/user-attachments/assets/f0f26fbc-f411-4d00-93c3-9d1b5017cf46" />

> *Figure 15: Extracted credentials.*

---

### **Q9: Malicious Script Uploaded by Attacker**
**Question:** We need to determine if the attacker gained further access or control of our web server. What's the name of the malicious script uploaded by the attacker?

**Method:**
- After successful login, the attacker may upload a backdoor. Look for POST requests containing file uploads (multipart/form-data).
- Filter POST requests from attacker and search for `filename=` or `.php`.

**Observation:**
- One POST request to `/admin/upload.php` (or similar) contains a PHP file named **`NVri2vhp.php`**.

**Answer:** `NVri2vhp.php`

<img width="975" height="256" alt="image" src="https://github.com/user-attachments/assets/a3595d5c-ae05-4cff-9c0d-4a3b1e41fa22" />

> *Figure 16: POST request showing file upload.*

<img width="975" height="802" alt="image" src="https://github.com/user-attachments/assets/80f666d4-7b82-42f8-a9cd-10c18d2e149d" />

> *Figure 17: Filename in the request.*

---

## **Indicators of Compromise (IOCs)**

| Type | Value |
|------|-------|
| Attacker IP | 111.224.250.131 |
| City of Origin | Shijiazhuang, China |
| Vulnerable Script | search.php |
| First SQLi URI | `/search.php?search=book and 1=1; -- -` |
| Database Enumeration URI | `/search.php?search=book' UNION ALL SELECT NULL,CONCAT(0x7178766271,JSON_ARRAYAGG(CONCAT_WS(0x7a76676a636b,schema_name)),0x7176706a71) FROM INFORMATION_SCHEMA.SCHEMATA-- -` |
| Users Table | customers |
| Hidden Directory | /admin/ |
| Compromised Credentials | admin:admin123! |
| Malicious Upload | NVri2vhp.php |

---

## **MITRE ATT&CK Mapping**

| Tactic | Technique | ID |
|--------|-----------|----|
| **Initial Access** | Exploit Public-Facing Application (SQLi) | T1190 |
| **Discovery** | File and Directory Discovery | T1083 |
| **Credential Access** | Brute Force | T1110 |
| **Persistence** | Web Shell | T1505.003 |
| **Command and Control** | Commonly Used Port (HTTP) | T1071.001 |

---

## **Conclusion**

The investigation of the web server compromise reveals a structured attack:
1. **Reconnaissance**: Attacker IP `111.224.250.131` scanned directories using `gobuster`, discovering `/admin/`.
2. **Initial Access**: SQL injection was performed on `search.php` to enumerate database structure and extract table names.
3. **Credential Access**: The attacker brute‑forced the admin login, successfully using `admin:admin123!`.
4. **Persistence**: A malicious PHP script `NVri2vhp.php` was uploaded, likely providing a backdoor for continued access.

These findings provide actionable IOCs and highlight the need for input validation, strong credentials, and monitoring of administrative interfaces.

---

## **Tools Used**
- **Wireshark**: Filtering, conversation statistics, HTTP stream following, packet inspection.
- **AbuseIPDB**: GeoIP lookup.
- **URL Decoder**: For decoding encoded request URIs.

---

## **References**
- CyberDefenders.org – Web Investigation Lab
- MITRE ATT&CK® Framework
- OWASP SQL Injection Cheat Sheet

---
