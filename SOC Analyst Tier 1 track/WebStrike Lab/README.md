# 🛡️ WebStrike Lab – SOC Analyst Tier 1

## 📌 Lab Overview
**Scenario:** Analyze network traffic using Wireshark to investigate a web server compromise, identify web shell deployment, reverse shell communication, and data exfiltration.  
**Category:** Network Forensics  
**Tactics:** Initial Access, Execution, Persistence, Command and Control, Exfiltration  
**Tools:** Wireshark  
**Difficulty:** Easy | Retired | ~30 mins  
**Region:** Frankfurt  

---

## 🧩 Objectives
1. Investigate network traffic for signs of compromise  
2. Identify malicious web shell and its communication  
3. Detect data exfiltration attempts  
4. Document findings for SOC reporting  

---

## 📝 Steps & Walkthrough

### 🔹 Step 1: Initial Traffic Inspection
*Quick Summary:* Open `WebStrike.pcap` in Wireshark and review IPv4 statistics and source IPs to establish baseline activity.  
**Screenshot:**  
![Step 1](step1.png)

### 🔹 Step 2: Identify Attack Origin
*Quick Summary:* Trace the source IP to determine the geographical origin using IP geolocation.  
**Screenshot:**  
![Step 2](step2.png)

### 🔹 Step 3: Analyze User-Agent
*Quick Summary:* Examine HTTP requests to extract the attacker's full User-Agent string.  
**Screenshot:**  
![Step 3](step3.png)

### 🔹 Step 4: Detect Malicious Web Shell
*Quick Summary:* Locate uploaded malicious files and note the web shell name (`image.jpg.php`).  
**Screenshot:**  
![Step 4](step4.png)

### 🔹 Step 5: Identify Upload Directory
*Quick Summary:* Determine the directory used for storing uploaded files (`/reviews/uploads/`).  
**Screenshot:**  
![Step 5](step5.png)

### 🔹 Step 6: Check Outbound Communication
*Quick Summary:* Identify the port used by the web shell for reverse shell connections (e.g., `8080`).  
**Screenshot:**  
![Step 6](step6.png)

### 🔹 Step 7: Data Exfiltration
*Quick Summary:* Detect which files the attacker attempted to exfiltrate (`passwd`).  
**Screenshot:**  
![Step 7](step7.png)

---

## 🧾 Questions & Flags
| Question | Answer |
|----------|--------|
| Q1 – Attack Origin | Tianjin |
| Q2 – Full User-Agent | Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0 |
| Q3 – Malicious Web Shell | image.jpg.php |
| Q4 – Upload Directory | /reviews/uploads/ |
| Q5 – Target Port | 8080 |
| Q6 – Exfiltrated File | passwd |

---

## 📂 Folder Structure
