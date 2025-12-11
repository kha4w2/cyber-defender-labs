<h1 align="center"><b>Red Stealer Lab </b></h1>

# **Overview**




## **Lab Information**

* **Lab Platform:** CyberDefenders
* **Lab Name:** Red Stealer
* **Lab Link:** [https://cyberdefenders.org/blueteam-ctf-challenges/red-stealer/](https://cyberdefenders.org/blueteam-ctf-challenges/red-stealer/)
* **Category:** Threat Intelligence
* **Tools Used:** VirusTotal, MalwareBazaar, ThreatFox
* **Difficulty:** Easy
* **Time:** 30 minutes

  <img width="1917" height="955" alt="image" src="https://github.com/user-attachments/assets/58e60c31-4a6a-4975-9e95-ef7169adb2d6" />


---

## **Lab Objective**

Analyze a suspicious executable using threat intelligence platforms to extract Indicators of Compromise (IOCs), identify C2 infrastructure, map MITRE ATT&CK techniques, and understand privilege escalation mechanisms.

---

## 🎯 **Question 1: Malware Categorization**

### 🔍 **Step 1: Hash Submission to VirusTotal**


<img width="975" height="202" alt="image" src="https://github.com/user-attachments/assets/10533c8e-f9e7-4e6d-a3db-0462ae87e432" />


<img width="975" height="453" alt="image" src="https://github.com/user-attachments/assets/c856bfae-5466-4848-b9ef-a2e7563b0891" />


**Action:** Submitted the malware SHA-256 hash to VirusTotal.
**Explanation:** Used to analyze the file across multiple security vendors.

---

### 🔍 **Step 2: Reviewing VirusTotal Detection Results**

<img width="975" height="465" alt="image" src="https://github.com/user-attachments/assets/be057545-fb2e-4f96-a6e0-b616fb7f7e94" />


**Action:** Checked the **Threat categories** section.
**Explanation:** Shows how vendors classify the malware.

---

### **Answer:**

<img width="975" height="234" alt="image" src="https://github.com/user-attachments/assets/e96dfde2-a8bb-4c38-91e0-09f6b3eefad2" />

**Trojan** 🛡️

---

## 🎯 **Question 2: Malware File Name Identification**



### 🔍 **Step: Locating the File Name**


<img width="975" height="465" alt="image" src="https://github.com/user-attachments/assets/7f66584a-4a5e-469e-a955-9d9d930fe104" />


**Action:** Checked the hash details section on VirusTotal.
**Explanation:** The filename is displayed at the top of the report.

---

### **Answer:**

<img width="975" height="291" alt="image" src="https://github.com/user-attachments/assets/a2ce5467-e991-42fd-98eb-3398efbf9e54" />


**WEXTRACT** 🛡️

---

## 🎯 **Question 3: Malware First Submission Timestamp**

### 🔍 **Step: Checking First Submission Timestamp**

<img width="975" height="456" alt="image" src="https://github.com/user-attachments/assets/e3ce7a8a-c4fc-4b7f-8175-92dbf67c2232" />

**Action:** Opened **Details → History** on VirusTotal.
**Explanation:** Identifies when the malware was first observed.

---

### **Answer:**


<img width="975" height="258" alt="image" src="https://github.com/user-attachments/assets/93776832-db3c-4301-a1a3-b0ffff84f9b7" />


**2023-10-06 04:41** 🛡️

---

## 🎯 **Question 4: MITRE ATT&CK Technique for Data Collection**

### 🔍 **Step 1: Navigating to Behavior Analysis**

Opened the **Behavior** tab on VirusTotal.

<img width="975" height="461" alt="image" src="https://github.com/user-attachments/assets/297692ff-11d3-4319-9541-0297cffab513" />


---

### 🔍 **Step 2: Reviewing MITRE ATT&CK Techniques**

<img width="975" height="470" alt="image" src="https://github.com/user-attachments/assets/fefbce7a-3fb3-4f21-9bcb-092f850ead88" />


Found **T1005 – Data from Local System** under the *Collection* tactic.

---

### **Answer:**

<img width="975" height="210" alt="image" src="https://github.com/user-attachments/assets/5a481eb4-ac9a-41a7-8d9c-32ec5982f078" />


**T1005** 🛡️

---

## 🎯 **Question 5: Malware DNS Resolutions for Social Media**

### 🔍 **Step 1: Navigating to Behavior Analysis**

Opened the **Behavior** tab.

---

### 🔍 **Step 2: Reviewing DNS Resolutions**

<img width="975" height="468" alt="image" src="https://github.com/user-attachments/assets/f3197b6e-f50b-4a1e-8624-e78e52987b38" />


Scrolled to **DNS Resolutions** and identified social media domains.

---

### **Answer:**


<img width="975" height="202" alt="image" src="https://github.com/user-attachments/assets/5d4a8caf-0997-4045-b51c-57f0b7dbc06b" />


**facebook.com** 🛡️

---

## 🎯 **Question 6: Malicious IP Address and Destination Port**

### 🔍 **Step 1: Checking Behavior Analysis**

Opened **Behavior → IP Traffic**.

---

### 🔍 **Step 2: Identifying IP and Port**


<img width="975" height="466" alt="image" src="https://github.com/user-attachments/assets/98b6ed1a-c76f-4c36-9b9d-fbd43faa90cf" />


Detected the C2 IP & port used by the malware.

---

### **Answer:**

<img width="975" height="259" alt="image" src="https://github.com/user-attachments/assets/c1c03917-3dd0-4341-9174-bea0f03091f0" />


**77.91.124.55:19071** 🛡️

---

## 🎯 **Question 7: YARA Rule for Malware Detection**

### 🔍 **Step 1: Searching MalwareBazaar**

Searched the malware hash on MalwareBazaar.

<img width="975" height="449" alt="image" src="https://github.com/user-attachments/assets/87c0772c-75a5-497e-8455-4350875e189b" />

<img width="975" height="451" alt="image" src="https://github.com/user-attachments/assets/90a3d98a-d5d6-4e84-a3f1-9c2ede1ac662" />

<img width="975" height="463" alt="image" src="https://github.com/user-attachments/assets/1b1c2e35-854a-4cde-b4d8-dd9220dd5aa0" />



---

### 🔍 **Step 2: Locating YARA Rule**

<img width="975" height="482" alt="image" src="https://github.com/user-attachments/assets/e94f539a-34bc-4004-9c80-ce2fa4953e5e" />


Used **Ctrl+F** to search for *Varp0s* and found the YARA rule.

---

### **Answer:**

<img width="975" height="221" alt="image" src="https://github.com/user-attachments/assets/28828b58-730b-4a1e-94d0-d6141af250d6" />


**detect_Redline_Stealer** 🛡️

---

## 🎯 **Question 8: Malware Alias Associated with Malicious IP**

### 🔍 **Step 1: Searching ThreatFox**

<img width="975" height="471" alt="image" src="https://github.com/user-attachments/assets/635c5244-36d1-41f7-8cd3-575a39de6710" />


Searched IP **77.91.124.55** on ThreatFox.

---

### 🔍 **Step 2: Reviewing Malware Alias**

Found the alias linked to the IP.

<img width="975" height="488" alt="image" src="https://github.com/user-attachments/assets/48f61c0c-7eb9-4be7-9306-2a516f294e30" />


---

### **Answer:**

<img width="975" height="254" alt="image" src="https://github.com/user-attachments/assets/6f58ed70-d3aa-4f17-80ff-83336a104c2d" />


**RECORDSTEALER** 🛡️

---

## 🎯 **Question 9: DLL Used for Privilege Escalation**

### 🔍 **Step 1: Reviewing Imported DLLs**

Opened **VirusTotal → Details → Imports**.

---

### 🔍 **Step 2: Identifying Privilege Escalation Indicators**


<img width="975" height="387" alt="image" src="https://github.com/user-attachments/assets/e7bfbdab-5af8-4b7e-ae24-feedd76be34a" />



Privilege escalation APIs like:

* `AdjustTokenPrivileges`
* `OpenProcessToken`
* `LookupPrivilegeValueA`

All loaded from **ADVAPI32.dll**.

---

### **Answer:**

<img width="975" height="325" alt="image" src="https://github.com/user-attachments/assets/c494c721-e193-4fbe-b6ee-be390bc16c58" />


**ADVAPI32.dll**

---

## **Analysis Summary**

A malicious sample was analyzed and classified as a **Trojan (Redline Stealer)**. Key findings:

1. **Classification:** Trojan – WEXTRACT
2. **Timeline:** First seen on 2023-10-06
3. **Technique Used:** T1005 (Data from Local System)
4. **C2 Infrastructure:**

   * Domain: facebook.com
   * IP: 77.91.124.55:19071
5. **Detection Rule:** detect_Redline_Stealer (YARA)
6. **Malware Alias:** RECORDSTEALER
7. **Privilege Escalation:** ADVAPI32.dll

---

## **Cybersecurity Importance**

Using platforms like VirusTotal, MalwareBazaar, and ThreatFox enhances SOC capability to:

* Enrich threat intelligence
* Block malicious infrastructure
* Create new detection signatures
* Map attacker behavior using MITRE ATT&CK

---

**Result:** ✅ Full and comprehensive threat intelligence report successfully completed.

<img width="1917" height="955" alt="image" src="https://github.com/user-attachments/assets/2b3a327a-7389-4f11-bc98-2e8b85c32af8" />


---
