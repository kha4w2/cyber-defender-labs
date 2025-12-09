<h1 align="center"><b>Red Stealer Lab - Overview Report</b></h1>

# **Red Stealer Lab - Overview Report**

## **Lab Information**

* **Lab Platform:** CyberDefenders
* **Lab Name:** Red Stealer
* **Lab Link:** [https://cyberdefenders.org/blueteam-ctf-challenges/red-stealer/](https://cyberdefenders.org/blueteam-ctf-challenges/red-stealer/)
* **Category:** Threat Intelligence
* **Tools Used:** VirusTotal, MalwareBazaar, ThreatFox
* **Difficulty:** Easy
* **Time:** 30 minutes

---

## **Lab Objective**

Analyze a suspicious executable using threat intelligence platforms to extract Indicators of Compromise (IOCs), identify C2 infrastructure, map MITRE ATT&CK techniques, and understand privilege escalation mechanisms.

---

## 🎯 **Question 1: Malware Categorization**

### 🔍 **Step 1: Hash Submission to VirusTotal**

**Action:** Submitted the malware SHA-256 hash to VirusTotal.
**Explanation:** Used to analyze the file across multiple security vendors.

---

### 🔍 **Step 2: Reviewing VirusTotal Detection Results**

**Action:** Checked the **Threat categories** section.
**Explanation:** Shows how vendors classify the malware.

---

### **Answer:**

**Trojan** 🛡️

---

## 🎯 **Question 2: Malware File Name Identification**

### 🔍 **Step 2: Locating the File Name**

**Action:** Checked the hash details section on VirusTotal.
**Explanation:** The filename is displayed at the top of the report.

---

### **Answer:**

**WEXTRACT** 🛡️

---

## 🎯 **Question 3: Malware First Submission Timestamp**

### 🔍 **Step 2: Checking First Submission Timestamp**

**Action:** Opened **Details → History** on VirusTotal.
**Explanation:** Identifies when the malware was first observed.

---

### **Answer:**

**2023-10-06 04:41** 🛡️

---

## 🎯 **Question 4: MITRE ATT&CK Technique for Data Collection**

### 🔍 **Step 1: Navigating to Behavior Analysis**

Opened the **Behavior** tab on VirusTotal.

---

### 🔍 **Step 2: Reviewing MITRE ATT&CK Techniques**

Found **T1005 – Data from Local System** under the *Collection* tactic.

---

### **Answer:**

**T1005** 🛡️

---

## 🎯 **Question 5: Malware DNS Resolutions for Social Media**

### 🔍 **Step 1: Navigating to Behavior Analysis**

Opened the **Behavior** tab.

---

### 🔍 **Step 2: Reviewing DNS Resolutions**

Scrolled to **DNS Resolutions** and identified social media domains.

---

### **Answer:**

**facebook.com** 🛡️

---

## 🎯 **Question 6: Malicious IP Address and Destination Port**

### 🔍 **Step 1: Checking Behavior Analysis**

Opened **Behavior → IP Traffic**.

---

### 🔍 **Step 2: Identifying IP and Port**

Detected the C2 IP & port used by the malware.

---

### **Answer:**

**77.91.124.55:19071** 🛡️

---

## 🎯 **Question 7: YARA Rule for Malware Detection**

### 🔍 **Step 1: Searching MalwareBazaar**

Searched the malware hash on MalwareBazaar.

---

### 🔍 **Step 2: Locating YARA Rule**

Used **Ctrl+F** to search for *Varp0s* and found the YARA rule.

---

### **Answer:**

**detect_Redline_Stealer** 🛡️

---

## 🎯 **Question 8: Malware Alias Associated with Malicious IP**

### 🔍 **Step 1: Searching ThreatFox**

Searched IP **77.91.124.55** on ThreatFox.

---

### 🔍 **Step 2: Reviewing Malware Alias**

Found the alias linked to the IP.

---

### **Answer:**

**RECORDSTEALER** 🛡️

---

## 🎯 **Question 9: DLL Used for Privilege Escalation**

### 🔍 **Step 1: Reviewing Imported DLLs**

Opened **VirusTotal → Details → Imports**.

---

### 🔍 **Step 2: Identifying Privilege Escalation Indicators**

Privilege escalation APIs like:

* `AdjustTokenPrivileges`
* `OpenProcessToken`
* `LookupPrivilegeValueA`

All loaded from **ADVAPI32.dll**.

---

### **Answer:**

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

---
