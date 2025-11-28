# 🛡️ WebStrike Lab – SOC Analyst Tier 1

## 📌 Lab Overview

**📝 Scenario:** Analyze network traffic using **Wireshark** to investigate a web server compromise, detect malicious web shell deployment, track reverse shell communication, and examine data exfiltration attempts.  

**🗂️ Category:** Network Forensics  

**🎯 Tactics:** Initial Access, Execution, Persistence, Command and Control, Exfiltration  

**🛠️ Tools:** Wireshark  

**⚡ Difficulty:** Easy | ⏱ ~30 mins

---

# 🧩 Walkthrough per Question

---

## 🔹 **Q1 – Identify Attack Origin**

### 🔹 **Step 1 – Reviewing IPv4 Conversations 📡**

*Loaded the PCAP file and inspected the **IPv4 Conversations** to identify the most active source IP involved in the traffic.*
 
<img width="1917" height="896" alt="s 1 2" src="https://github.com/user-attachments/assets/38d4bf20-68b0-4a19-bda8-929bed0b51b0" />


### 🔎 Step 2 – IP Geolocation Lookup 🌍

**Used an external IP lookup service to identify the geographical origin of the attacker’s IP **117.11.88.124**, confirming it traces back to **Tianjin, China**.

<img width="1141" height="802" alt="Screenshot 2025-11-28 155705" src="https://github.com/user-attachments/assets/875a6827-f51a-40ab-a217-ab436ad90c00" />
<img width="1102" height="342" alt="Screenshot 2025-11-28 153347" src="https://github.com/user-attachments/assets/486e9da2-f3d6-4e59-988a-0998b15d563e" />



---

## Q2 – Identifying the Attacker’s User-Agent 🕵️‍♂️

I inspected **TCP Stream 0** to view the attacker’s initial HTTP request.  
By analyzing the GET request headers, I extracted the complete **User-Agent** string used during the intrusion attempt.

<img width="1910" height="912" alt="2" src="https://github.com/user-attachments/assets/979e5ab0-4ec1-4ade-ba5b-2b7e6a779bd7" />

<img width="1916" height="921" alt="image" src="https://github.com/user-attachments/assets/b5480338-df36-4b9f-9290-1dce3cef226e" />

<img width="1122" height="267" alt="Screenshot 2025-11-28 153402" src="https://github.com/user-attachments/assets/67745b5d-f5bb-41f7-ad6f-e878e44371ca" />



---

## Q3 – Identifying the Malicious Web Shell 🐚💥🔎

To confirm whether the vulnerability was exploited, I applied the filter  
`ip.addr == 117.11.88.124 && http` to isolate all attacker-originating HTTP traffic.  
By inspecting the malicious POST request toward **/reviews/upload.php** and following  
the full HTTP stream, the server replied with **"File uploaded successfully"**, exposing  
the uploaded web shell name hidden inside the request payload. ⚠️📡

**Answer:** `image.jpg.php` 🧨
<img width="1918" height="697" alt="Screenshot 2025-11-28 151304" src="https://github.com/user-attachments/assets/5231e62d-1a9f-47d1-a61c-4b2b89c291b9" />

<img width="1918" height="916" alt="Screenshot 2025-11-28 151328" src="https://github.com/user-attachments/assets/69e45695-73e5-4024-9e2c-f65c768be20b" />

<img width="1918" height="922" alt="Screenshot 2025-11-28 151353" src="https://github.com/user-attachments/assets/5c303d6b-d3ac-4f71-9a9c-c283e7ef2ba3" />

<img width="1918" height="866" alt="Screenshot 2025-11-28 151449" src="https://github.com/user-attachments/assets/0f5120b9-fe2f-4637-b5ba-5c22dc3fdcb8" />

<img width="1172" height="249" alt="Screenshot 2025-11-28 153434" src="https://github.com/user-attachments/assets/60571a46-903e-4e09-91d9-db720258f375" />


---

## Q4 – Identifying the File Upload Directory 📁🚨🕵️‍♂️

To locate where the attacker’s uploaded script was stored, I followed the POST request  
containing the malicious file *image.jpg.php*. Inspecting the HTTP request path revealed  
that uploads were handled through **/reviews/upload.php**, which stores files inside the  
**/reviews/uploads/** directory. 📂🔥

**Answer:** `/reviews/uploads/` ✔️

<img width="1918" height="903" alt="Screenshot 2025-11-28 152322" src="https://github.com/user-attachments/assets/1b66bfff-08e1-47a0-af58-0eef87a16243" />

<img width="1135" height="261" alt="Screenshot 2025-11-28 153502" src="https://github.com/user-attachments/assets/6e9b3af3-a0b8-46ab-86a3-5e978e3b8a32" />

---

## Q5 – Identifying the Malicious Web Shell Port 🔍💻🔥

To determine which port the attacker’s web shell used, I inspected the POST request that  
contained the uploaded malicious file. Inside the PHP payload, the reverse shell command  
revealed the **nc (netcat)** connection, clearly showing the outbound port **8080**, which the  
attacker used to establish their remote access. 🚨📡

**Answer:** `8080` ✔️

<img width="1918" height="903" alt="Screenshot 2025-11-28 152322" src="https://github.com/user-attachments/assets/7f13126d-d4e5-415f-98d3-8c930f9c3505" />

<img width="1130" height="258" alt="Screenshot 2025-11-28 153522" src="https://github.com/user-attachments/assets/991bebb9-069e-4f8b-98dc-9226ab03f1dc" />

---

## Q6 – 💾 Investigating Data Breach: Pinpointing Targeted Files 🔍💻🔥

To see this response, find the packet that has the source IP 24.49.63.79 and destination IP 117.11.88.124 using the POST method. 🚨📡 

**Answer:** ` passwd` ✔️
<img width="1918" height="863" alt="Screenshot 2025-11-28 152923" src="https://github.com/user-attachments/assets/2ba54dd8-4b25-466b-95f0-b8601ccd6752" />

<img width="1107" height="323" alt="Screenshot 2025-11-28 153540" src="https://github.com/user-attachments/assets/d49dbf82-9396-4dea-a43b-1b02b7b45bd3" />

---

# 🧾 Questions & Flags
| Question | Answer |
|----------|--------|
| Q1 – Attack Origin | **Tianjin** |
| Q2 – Full User-Agent | **Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0** |
| Q3 – Malicious Web Shell | **image.jpg.php** |
| Q4 – Upload Directory | **/reviews/uploads/** |
| Q5 – Target Port | **8080** |
| Q6 – Exfiltrated File | **passwd** |

---

## 📸 Completing This Lab 


<img width="1918" height="931" alt="Screenshot 2025-11-28 153328" src="https://github.com/user-attachments/assets/76a1cf58-379e-4806-b61d-f4c3004370d1" />

---
