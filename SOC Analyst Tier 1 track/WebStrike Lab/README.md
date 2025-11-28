<img width="1916" height="921" alt="2 1" src="https://github.com/user-attachments/assets/ab04a0a9-f520-472c-bd48-bf9776587064" /># 🛡️ WebStrike Lab – SOC Analyst Tier 1

## 📌 Lab Overview

**📝 Scenario:** Analyze network traffic using **Wireshark** to investigate a web server compromise, detect malicious web shell deployment, track reverse shell communication, and examine data exfiltration attempts.  

**🗂️ Category:** Network Forensics  
**🎯 Tactics:** Initial Access, Execution, Persistence, Command and Control, Exfiltration  
**🛠️ Tools:** Wireshark  
**⚡ Difficulty:** Easy | ⏱ ~30 mins

---

# 🧩 Walkthrough per Question

---

## 🔹 **QUES.1 – Identify Attack Origin**
### 🔹 **Step 1 – Reviewing IPv4 Conversations 📡**
*Loaded the PCAP file and inspected the **IPv4 Conversations** to identify the most active source IP involved in the traffic.*
 
<img width="1917" height="896" alt="s 1 2" src="https://github.com/user-attachments/assets/38d4bf20-68b0-4a19-bda8-929bed0b51b0" />


### 🔎 Step 2 – IP Geolocation Lookup 🌍
**Summary:** Used an external IP lookup service to identify the geographical origin of the attacker’s IP **117.11.88.124**, confirming it traces back to **Tianjin, China**.
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

## 🔹 **QUES.3 – Identify Uploaded Web Shell**
### **Step 1:** Filter HTTP POST Uploads  
*Focused on POST requests from attacker’s IP to locate malicious file uploads.*  
**Screenshot:**  
![Q3-Step1](q3-step1.png)

### **Step 2:** Web Shell Detection  
*Confirmed the uploaded malicious file name (`image.jpg.php`).*  
**Screenshot:**  
![Q3-Step2](q3-step2.png)

---

## 🔹 **QUES.4 – Determine Upload Directory**
### **Step 1:** Follow Web Shell Execution Path  
*Tracked the web shell request to uncover its directory location.*  
**Screenshot:**  
![Q4-Step1](q4-step1.png)

### **Step 2:** Directory Confirmation  
*Identified the upload folder used by the server: `/reviews/uploads/`.*  
**Screenshot:**  
![Q4-Step2](q4-step2.png)

---

## 🔹 **QUES.5 – Outbound Communication Port**
### **Step 1:** Inspect Malicious POST Content  
*Checked the uploaded script content to view reverse shell configuration.*  
**Screenshot:**  
![Q5-Step1](q5-step1.png)

### **Step 2:** Port Identification  
*Found attacker’s listening port used for outbound connection (`8080`).*  
**Screenshot:**  
![Q5-Step2](q5-step2.png)

---

## 🔹 **QUES.6 – Detect Exfiltrated File**
### **Step 1:** Follow POST Traffic to Attacker  
*Filtered outbound POST requests targeting attacker’s server.*  
**Screenshot:**  
![Q6-Step1](q6-step1.png)

### **Step 2:** File Exfiltration Analysis  
*Confirmed the target file intended for exfiltration (`passwd`).*  
**Screenshot:**  
![Q6-Step2](q6-step2.png)

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

## 📸 Screenshot Example  


<img width="1918" height="931" alt="Screenshot 2025-11-28 153328" src="https://github.com/user-attachments/assets/76a1cf58-379e-4806-b61d-f4c3004370d1" />

---
