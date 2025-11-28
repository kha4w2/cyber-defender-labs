# 🛡️ WebStrike Lab – SOC Analyst Tier 1

## 📌 Lab Overview
**Scenario:** Analyze network traffic using Wireshark to investigate a web server compromise, identify web shell deployment, reverse shell communication, and data exfiltration.  
**Category:** Network Forensics  
**Tactics:** Initial Access, Execution, Persistence, Command and Control, Exfiltration  
**Tools:** Wireshark  
**Difficulty:** Easy | ~30 mins  

---

# 🧩 Walkthrough per Question

---

## 🔹 **QUES.1 – Identify Attack Origin**
### **Step 1:** Initial Traffic Inspection  
*Opened `WebStrike.pcap` in Wireshark and reviewed top source IPs to identify suspicious traffic.*  
**Screenshot:**  
![Q1-Step1](q1-step1.png)

### **Step 2:** Geolocation Lookup  
*Queried the main suspicious IP using external geolocation to determine the originating city.*  
**Screenshot:**  
![Q1-Step2](q1-step2.png)

---

## 🔹 **QUES.2 – Extract Attacker User-Agent**
### **Step 1:** Filter HTTP GET Requests  
*Applied `http.request.method == "GET"` to isolate attacker’s browsing activity.*  
**Screenshot:**  
![Q2-Step1](q2-step1.png)

### **Step 2:** User-Agent Identification  
*Checked packet details to extract the full User-Agent string.*  
**Screenshot:**  
![Q2-Step2](q2-step2.png)

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
