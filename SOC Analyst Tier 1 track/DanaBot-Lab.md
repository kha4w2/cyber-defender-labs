# DanaBot Lab Write-Up: Network Traffic Analysis & Malware Investigation

<img width="1918" height="820" alt="image" src="https://github.com/user-attachments/assets/4461e368-7698-4589-aead-f15500b7b8c9" />

## **Lab Overview**
- **Platform**: CyberDefenders
- **Category**: Network Forensics
- **Tactics**: Execution, Command and Control
- **Tools Used**: Wireshark, VirusTotal, Kali Linux
- **Difficulty**: Easy
- **Scenario**: SOC team detected suspicious network activity indicating a compromised machine and stolen sensitive data. Tasked to investigate the breach using PCAP analysis and threat intelligence.
- **Lab Link**: https://cyberdefenders.org/blueteam-ctf-challenges/danabot/
---

## **Methodology & Investigation Steps**

### **1. Identifying the Victim Machine**

**Objective**: Determine the compromised host IP address.

**Analysis**:
- Opened the PCAP file in Wireshark
- Navigated to **Statistics > Conversations**
- Sorted conversations by **Bytes** (descending) to identify the most active hosts

**Finding**:
- The IP address `10.2.14.101` showed the highest traffic volume
- **Conclusion**: This is the victim machine IP

<img width="975" height="508" alt="image" src="https://github.com/user-attachments/assets/f8bdf816-1d3d-4eee-a982-9fdf91d0dc4d" />

> *Figure 1: Wireshark conversations sorted by bytes showing victim IP 10.2.14.101*

---

### **2. Identifying Initial Access Vector**

**Objective**: Determine how the initial compromise occurred.

**Analysis**:
- Applied display filter: `ip.addr == 10.2.14.101 && http`
- Examined HTTP traffic to/from victim
- Observed successful login attempts from external IP

**Finding**:
- Source IP `62.173.142.148` initiated HTTP connections to victim
- **Initial Access IP**: 62.173.142.148 (attacker infrastructure)

<img width="975" height="204" alt="image" src="https://github.com/user-attachments/assets/1934fcd7-595c-48e1-bbd8-98bd4898e1b7" />


> *Figure 2: HTTP traffic filtered for victim IP showing attacker connections*

---

### **3. Analyzing HTTP Stream & Malicious Payload**

**Objective**: Examine the HTTP conversation containing the initial payload.

**Analysis**:
- Followed **HTTP Stream** for suspicious packets
- Reviewed response data from attacker server
- Identified an attachment being downloaded

**Finding**:
- Malicious file name: **`allegato_708.js`** (JavaScript attachment)
- This file was delivered via HTTP response and represents the initial infection vector

<img width="975" height="342" alt="image" src="https://github.com/user-attachments/assets/fd752bea-781c-4acb-bfd1-acc17c8936e6" />


> *Figure 3: HTTP stream showing JavaScript attachment delivery*

---

### **4. Extracting Malicious File & Calculating Hash**

**Objective**: Obtain the malicious file and generate its hash for IOC documentation.

**Process**:
1. **Exported** the JavaScript file from Wireshark (File > Export Objects > HTTP)
2. Transferred file to **Kali Linux VM** (isolated environment for safety)
3. Calculated SHA-256 hash using: `sha256sum allegato_708.js`

**Finding**:
```
SHA-256: 847b4ad90b1daba2d9117a8e05776f3f902dda593fb1252289538acf476c4268
```
<img width="975" height="409" alt="image" src="https://github.com/user-attachments/assets/857289de-6ca6-4362-8b22-0bdab2bc1c44" />

> *Figure 4: Exporting HTTP objects from Wireshark*

<img width="975" height="682" alt="image" src="https://github.com/user-attachments/assets/f8757742-a761-4498-b45e-fd661a76b9e8" />

> *Figure 5: Transferring file to Kali Linux*

<img width="975" height="469" alt="image" src="https://github.com/user-attachments/assets/03154d0a-1731-49e0-b542-70de4bf894d1" />

> *Figure 6: Calculating file hash in Kali*

---

### **5. Identifying Execution Process from JavaScript**

**Objective**: Determine which Windows process executes the malicious script.

**Analysis**:
- Examined JavaScript code content
- Found reference to **`wscript`** (Windows Script Host)

**Finding**:
- The JavaScript is designed to execute via **`wscript.exe`**
- This is a common technique for executing malicious scripts on Windows systems

<img width="975" height="448" alt="image" src="https://github.com/user-attachments/assets/3fdc7c24-da2c-4ccd-aaab-94d17132830d" />

> *Figure 7: JavaScript code revealing wscript execution*

---

### **6. Threat Intelligence Validation**

**Objective**: Confirm malicious nature and gather additional context.

**Process**:
- Submitted hash to **VirusTotal**
- Analyzed detection results and behavior reports

**Findings**:
- **File is malicious** (multiple AV detections)
- Confirmed execution process: **`wscript.exe`**
- Additional threat intelligence gathered about DanaBot malware family

<img width="975" height="407" alt="image" src="https://github.com/user-attachments/assets/3719a424-4181-46c6-9926-1f483de88962" />

> *Figure 8: VirusTotal scan results*

<img width="975" height="431" alt="image" src="https://github.com/user-attachments/assets/ab79b018-df0d-41a5-bedf-fe31f515303c" />

> *Figure 9: VirusTotal behavior details confirming wscript.exe*

---

### **7. Identifying Additional Payloads**

**Objective**: Check if attacker downloaded more files after initial compromise.

**Analysis**:
- Continued examining HTTP traffic from victim
- Filtered for additional file downloads/transfers
- Identified second suspicious file

**Finding**:
- Secondary file: **`resources.dll`** (DLL file)
- This represents additional malware component or payload

<img width="975" height="150" alt="image" src="https://github.com/user-attachments/assets/3452f0a5-e23b-464e-bb06-5210dafa4bf8" />

> *Figure 10: HTTP traffic showing second file download*

---

### **8. Extracting Second File Hash**

**Objective**: Document IOC for secondary payload.

**Process**:
- Exported `resources.dll` from Wireshark
- Calculated SHA-256 hash

**Finding**:
```
SHA-256: 2597322a49a6252445ca4c8d713320b238113b3b8fd8a2d6fc1088a5934cee0e
```
<img width="975" height="482" alt="image" src="https://github.com/user-attachments/assets/9f385801-28b2-4562-bb5e-c7cccafd92a6" />

> *Figure 11: Exporting resources.dll from Wireshark*

<img width="975" height="427" alt="image" src="https://github.com/user-attachments/assets/45b6a319-4d83-417c-a51b-4bb9abbb5080" />

> *Figure 12: Calculating DLL file hash*

---

## **Indicators of Compromise (IOCs)**

| Type | Value |
|------|-------|
| **Victim IP** | 10.2.14.101 |
| **Attacker IP** | 62.173.142.148 |
| **Malicious File 1** | allegato_708.js |
| **File 1 SHA-256** | 847b4ad90b1daba2d9117a8e05776f3f902dda593fb1252289538acf476c4268 |
| **Malicious File 2** | resources.dll |
| **File 2 SHA-256** | 2597322a49a6252445ca4c8d713320b238113b3b8fd8a2d6fc1088a5934cee0e |
| **Execution Process** | wscript.exe |
| **Malware Family** | DanaBot |

---

## **Attack Chain Summary**

1. **Initial Access**: Attacker IP `62.173.142.148` delivered malicious JavaScript via HTTP
2. **Execution**: `allegato_708.js` executed by `wscript.exe` on victim machine
3. **Payload Delivery**: Additional component `resources.dll` downloaded post-infection
4. **Impact**: System compromised, data exfiltration possible (per scenario)

---

## **Tools Used**
- **Wireshark**: PCAP analysis, traffic filtering, object export
- **Kali Linux**: Safe environment for file handling
- **VirusTotal**: Threat intelligence and hash validation
- **sha256sum**: Hash calculation utility

---

## **References**
- CyberDefenders.org DanaBot Lab
- VirusTotal intelligence reports
- Windows Script Host (wscript) documentation

---
