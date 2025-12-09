# Red Stealer Lab - Overview Report

## **Lab Information**
- **Lab Platform:** CyberDefenders
- **Lab Name:** Red Stealer
- **Lab Link:** [https://cyberdefenders.org/blueteam-ctf-challenges/red-stealer/](https://cyberdefenders.org/blueteam-ctf-challenges/red-stealer/)
- **Category:** Threat Intelligence
- **Tools Used:** VirusTotal, MalwareBazaar, ThreatFox
- **Difficulty:** Easy
- **Time:** 30 minutes

## **Lab Objective**
Analyze a suspicious executable using threat intelligence platforms to extract Indicators of Compromise (IOCs), identify C2 infrastructure, map MITRE ATT&CK techniques, and understand privilege escalation mechanisms.

### 🎯 Question 1: Malware Categorization

---

### 🔍 Step 1: Hash Submission to VirusTotal

**Action:** Submitted the malware SHA-256 hash to VirusTotal.
**Explanation:** This allows us to analyze the file using multiple security vendors and gather its threat classification 💡

---

### 🔍 Step : Reviewing VirusTotal Detection Results

**Action:** Checked the "Threat categories" section in the analysis report.
**Explanation:** This section summarizes how security vendors classify the malware, providing insight into its behavior and attack type 🔎

---

### Answer:

**Trojan** 🛡️

### 🎯 Question 2: Malware File Name Identification

---

### 🔍 Step 2: Locating the File Name

**Action:** Checked the top section of the VirusTotal report under the hash details.
**Explanation:** The file name is listed there without the extension, which improves SOC communication and tracking 🔎

---

### Answer:

**WEXTRACT** 🛡️


### 🎯 Question 3: Malware First Submission Timestamp

---
🔍 Step 2: Checking First Submission Timestamp

Action: Went to the Details section and reviewed the History section in the VirusTotal report.
Explanation: The "First Submission" field shows when the malware was first observed, helping prioritize SOC response 🔎
---

### Answer:

**2023-10-06 04:41** 🛡️

### 🎯 Question 4: MITRE ATT&CK Technique for Data Collection

---

### 🔍 Step 1: Navigating to Behavior Analysis

**Action:** Opened the malware's **Behavior** page in VirusTotal.
**Explanation:** This section provides detailed information on the malware's actions and mapped MITRE ATT&CK techniques 💡

---

### 🔍 Step 2: Reviewing MITRE ATT&CK Tactics and Techniques

**Action:** Scrolled to the **MITRE ATT&CK Tactics and Techniques** section, focused on the **Collection** tactic, and identified the technique **Data from Local System** with ID **T1005** 🔎
**Explanation:** Understanding this technique helps the SOC plan strategic defenses against data exfiltration.

---

### Answer:

**T1005** 🛡️


### 🎯 Question 5: Malware DNS Resolutions for Social Media

---

### 🔍 Step 1: Navigating to Behavior Analysis

**Action:** Opened the malware's **Behavior** page in VirusTotal.
**Explanation:** This section provides details on all network activities, including DNS queries made by the malware 💡

---

### 🔍 Step 2: Reviewing DNS Resolutions

**Action:** Scrolled to the **DNS Resolutions** section and identified social media-related domains.
**Explanation:** This shows which domains the malware attempts to contact, helping SOC teams understand C2 and data exfiltration paths 🔎

---

### Answer:

**facebook.com** 🛡️


### 🎯 Question 6: Malicious IP Address and Destination Port

---

### 🔍 Step 1: Navigating to Behavior Analysis

**Action:** Opened the malware's **Behavior** page in VirusTotal.
**Explanation:** This section provides detailed network activity, including IP traffic and ports used by the malware 💡

---

### 🔍 Step 2: Reviewing IP Traffic

**Action:** Scrolled to the **IP Traffic** section and identified the malicious IP address and associated destination port.
**Explanation:** Knowing these values allows SOC teams to configure firewalls and block malicious communications 🔎

---

### Answer:

**77.91.124.55:19071** 🛡️

### 🎯 Question 7: YARA Rule for Malware Detection

---

### 🔍 Step 1: Searching MalwareBazaar

**Action:** Opened **MalwareBazaar** and searched using the malware SHA-256 hash.
**Explanation:** This allows us to locate the specific malware entry and related intelligence, including YARA rules 💡

---

### 🔍 Step 2: Locating YARA Rule

**Action:** On the malware entry page, used **Ctrl+F** to search for **Varp0s** and found the corresponding YARA rule.
**Explanation:** Identifying the YARA rule enables SOC teams to detect this malware pattern efficiently 🔎

---

### Answer:

**detect_Redline_Stealer** 🛡️


### 🎯 Question 8: Malware Alias Associated with Malicious IP

---

### 🔍 Step 1: Searching ThreatFox

**Action:** Searched for the malicious IP **77.91.124.55** on **ThreatFox**.
**Explanation:** This platform provides intelligence on IOCs and malware families linked to IP addresses, helping SOC teams identify threats 💡

---

### 🔍 Step 2: Reviewing Malware Alias

**Action:** Opened the IOC entry for the IP and checked the **Malware alias** field.
**Explanation:** Identifying the alias provides insight into which malware families are targeting the organization 🔎

---

### Answer:

**RECORDSTEALER** 🛡️

### 🎯 Question 9: DLL Used for Privilege Escalation

---

### 🔍 **Step 1: Reviewing Imported DLLs**

**Action:** Opened the **VirusTotal** → **Details** tab → **Imports** section.
**Goal:** Identify which DLL is responsible for **privilege escalation functions**.

---

### 🔍 **Step 2: Identifying Privilege Escalation Indicators**

Checked for API functions related to privilege escalation, such as:

* `AdjustTokenPrivileges`
* `OpenProcessToken`
* `LookupPrivilegeValueA`

All these functions were imported from **ADVAPI32.dll**, which is typically used for manipulating access tokens → a common privilege escalation behavior.

---

### **Answer:**

**ADVAPI32.dll**


## **Analysis Summary**
A malicious sample was analyzed and classified as a **Trojan** of the **Redline Stealer** type, with all critical threat indicators identified including:

1. **Classification and Identity:** Trojan - WEXTRACT
2. **Timeline:** First observed 2023-10-06
3. **Techniques Used:** T1005 (Data from Local System)
4. **Threat Infrastructure:** 
   - C2 Domain: facebook.com
   - IP: 77.91.124.55:19071
5. **Detection Rules:** YARA rule: detect_Redline_Stealer
6. **Aliases:** RECORDSTEALER
7. **Privilege Escalation Mechanism:** Use of ADVAPI32.dll

## **Cybersecurity Importance**
This analysis demonstrates how to use Threat Intelligence platforms like VirusTotal, MalwareBazaar, and ThreatFox to analyze malware and extract IOCs that can be used for:
- Improving detection and prevention systems
- Updating firewall block lists
- Building new detection rules
- Understanding attacker tactics according to the MITRE ATT&CK framework

---

**Result:** ✅ Complete and comprehensive threat analysis with all required indicators successfully identified.
