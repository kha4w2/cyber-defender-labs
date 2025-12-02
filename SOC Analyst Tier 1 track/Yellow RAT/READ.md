
# 🟡 Yellow RAT – CyberDefenders Write-Up

### *Threat Intelligence – Malware Investigation*

---

## 📌 Scenario

During a routine IT security audit at **GlobalTech Industries**, analysts detected abnormal outbound traffic and browser search redirections across several workstations. Your task is to analyze the malware sample using threat intelligence platforms and identify key Indicators of Compromise (IOCs).

---

## 🛠️ Tools Used

* **VirusTotal**
* **Red Canary Threat Intelligence**

---

## ✔️ Questions & Answers

---

## **Q1 — Malware Family Identification**

<img width="975" height="472" alt="image" src="https://github.com/user-attachments/assets/47d65a69-b5a8-4c1f-911b-3063c354b5b5" />

<img width="975" height="474" alt="image" src="https://github.com/user-attachments/assets/8b9813f6-25b6-4dd9-ad70-02b64c5942ea" />

<img width="975" height="220" alt="image" src="https://github.com/user-attachments/assets/46983b81-b458-46c9-a093-4f6732f96b7b" />

**Answer:** `Yellow Cockatoo RAT`

**Method:**
Identified malware family by searching the provided hash on VirusTotal and confirming via Community comments.

---

## **Q2 — Common Malware Filename**

<img width="975" height="288" alt="image" src="https://github.com/user-attachments/assets/9ccd8994-f5b8-4488-a729-244bd1ec503c" />

<img width="975" height="426" alt="image" src="https://github.com/user-attachments/assets/9e7fd048-e1de-401b-ab67-9faa23003ab0" />

<img width="975" height="222" alt="image" src="https://github.com/user-attachments/assets/4836761b-bb3c-4dfc-b712-ec4c2322b5a9" />

**Answer:** `111bc461-1ca8-43c6-97ed-911e0e69fdf8.dll`

**Method:**
Extracted the malware's common filename from the “Details → Names” section on VirusTotal.

---

## **Q3 — Compilation Timestamp**

<img width="975" height="389" alt="image" src="https://github.com/user-attachments/assets/3829afc8-1949-4132-b2f2-aaf51bdad64d" />

<img width="975" height="218" alt="image" src="https://github.com/user-attachments/assets/f48f28d5-25ae-465b-9729-927b89e939d3" />

**Answer:** `2020-09-24 18:26`

**Method:**
Retrieved the compilation timestamp from the PE header under “Portable Executable Info” on VirusTotal.

---

## **Q4 — First Submission to VirusTotal**

<img width="975" height="210" alt="image" src="https://github.com/user-attachments/assets/3888d44c-d1f7-4226-8055-7a14da717666" />

<img width="975" height="250" alt="image" src="https://github.com/user-attachments/assets/5435463b-2cc7-4c41-801e-c6bfd9e6ce3a" />

**Answer:** `2020-10-15 02:47`

**Method:**
Checked the “History” tab on VirusTotal to find the first submission date of the sample.

---

## **Q5 — Dropped File in AppData**

<img width="975" height="395" alt="image" src="https://github.com/user-attachments/assets/f7555a8c-bd5f-4bf6-9f73-60d532fa0208" />

<img width="975" height="395" alt="image" src="https://github.com/user-attachments/assets/990f95d3-48b4-49f4-a6ff-41c8eedd0292" />

**Answer:** `solarmarker.dat`

**Method:**
Verified dropped artifacts using Red Canary’s analysis of Yellow Cockatoo/Jupyter malware.

---

## **Q6 — Command & Control (C2) Server**

<img width="975" height="482" alt="image" src="https://github.com/user-attachments/assets/81e945b0-c429-414e-a95d-a1cfc950aa12" />

<img width="975" height="261" alt="image" src="https://github.com/user-attachments/assets/0311890f-089c-4704-a462-0f964457392b" />

**Answer:** `https://gogohid.com`

**Method:**
Extracted the C2 endpoint from Red Canary’s threat intelligence documentation on SolarMarker/Yellow Cockatoo RAT.

---

## 📎 References

* VirusTotal Sample Analysis
* Red Canary: SolarMarker / Yellow Cockatoo Threat Report
* CyberDefenders Lab: **Yellow RAT**

---

## ✅ Notes

This write-up is designed to help SOC analysts and threat hunters quickly identify malware behavior, IOCs, and common artifacts associated with the **Yellow Cockatoo RAT** family.🔥 


# 🟡 Yellow RAT – Malware Analysis & Threat Intelligence Report

## 📋 Executive Summary
During a routine security audit at **GlobalTech Industries**, anomalous network activity and browser redirects were observed across multiple endpoints. Subsequent analysis identified a **Yellow Cockatoo RAT (Remote Access Trojan)** infection, part of the SolarMarker malware family. This report documents key findings, indicators of compromise (IOCs), and relevant threat intelligence.

---

## 🔍 Analysis Summary

### **Malware Overview**
- **Family:** Yellow Cockatoo RAT (SolarMarker/Jupyter variant)
- **Type:** Information-stealing remote access trojan
- **Primary Function:** Browser hijacking, credential harvesting, backdoor access
- **Delivery Method:** Typically via malicious SEO poisoning and fake software installers

### **Key Characteristics**
- Persists via registry modifications and dropped files
- Establishes C2 communication over HTTPS
- Modifies browser settings for search redirection
- Evades detection through obfuscation and legitimate-looking filenames

---

## 📊 Technical Findings

### **Q1: Malware Family Identification**
**Finding:** `Yellow Cockatoo RAT`  
**Source:** VirusTotal community analysis and detection labels  
**Confidence:** High (multiple AV vendors identify as Yellow Cockatoo/Jupyter/SolarMarker)

### **Q2: Common Filename**
**Finding:** `111bc461-1ca8-43c6-97ed-911e0e69fdf8.dll`  
**Location:** VirusTotal → Details → Names section  
**Note:** Uses UUID-style naming to appear legitimate

### **Q3: Compilation Timestamp**
**Finding:** `2020-09-24 18:26`  
**Source:** PE header information from VirusTotal analysis  
**Significance:** Helps establish malware timeline and campaign analysis

### **Q4: First Submission Date**
**Finding:** `2020-10-15 02:47`  
**Source:** VirusTotal History tab  
**Note:** ~3 weeks between compilation and first submission suggests private deployment period

### **Q5: Dropped Artifact**
**Finding:** `solarmarker.dat`  
**Location:** `%AppData%` directory  
**Purpose:** Configuration file or secondary payload storage

### **Q6: Command & Control Server**
**Finding:** `https://gogohid.com`  
**Source:** Red Canary threat intelligence reporting  
**Protocol:** HTTPS (encrypted C2 communication)

---

## 🎯 Indicators of Compromise (IOCs)

### **File Indicators**
- **SHA-256:** [Provided in challenge]
- **Filename:** `111bc461-1ca8-43c6-97ed-911e0e69fdf8.dll`
- **Dropped File:** `%AppData%\solarmarker.dat`

### **Network Indicators**
- **C2 Domain:** `gogohid.com`
- **Protocol:** HTTPS (port 443)

### **Behavioral Indicators**
- Browser search engine modifications
- Unusual outbound HTTPS traffic to unknown domains
- Registry changes for persistence
- File creation in AppData with .dat extension

---

## 🛡️ Recommended Actions

### **Immediate Containment**
1. Quarantine affected systems
2. Block C2 domain (`gogohid.com`) at network perimeter
3. Search for `solarmarker.dat` across endpoints
4. Check registry for suspicious autostart entries

### **Detection Rules**
```yaml
# YARA Rule (conceptual)
rule Yellow_Cockatoo_RAT {
    strings:
        $uuid_name = /[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}\.dll/
        $appdata_file = "solarmarker.dat"
    condition:
        any of them
}
```

### **Prevention Measures**
- Implement application whitelisting
- Monitor for suspicious .dll files with UUID names
- Enable HTTPS inspection for C2 detection
- Regular user awareness training on SEO poisoning tactics

---

## 📈 Threat Intelligence Context

### **Campaign Associations**
- Part of SolarMarker/Jupyter malware campaigns
- Active since at least 2020
- Targets various industries for credential theft
- Often distributed via "business document" lures

### **Tactics, Techniques & Procedures (TTPs)**
- **T1027:** Obfuscated Files or Information
- **T1059:** Command and Scripting Interpreter
- **T1071:** Application Layer Protocol (HTTPS)
- **T1112:** Modify Registry
- **T1547:** Boot or Logon Autostart Execution

---

## 🔗 References
1. VirusTotal Analysis: [Sample Link]
2. Red Canary Threat Report: SolarMarker/Yellow Cockatoo
3. MITRE ATT&CK: Techniques T1027, T1059, T1071, T1112, T1547
4. CyberDefenders Lab: Yellow RAT Challenge

---

## 📝 Analyst Notes
- The malware uses HTTPS for C2, making network detection more challenging
- UUID-style naming attempts to blend with legitimate system files
- The ~3-week gap between compilation and first submission suggests targeted deployment
- Browser redirects are a primary user-visible symptom

**Report Generated:** [Date]  
**Classification:** CONFIDENTIAL  
**TLP:** AMBER
