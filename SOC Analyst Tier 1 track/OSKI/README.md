# 🔍 OSKI Malware Analysis – Threat Hunting Lab

## 📌 Lab Overview

<img width="1911" height="926" alt="image" src="https://github.com/user-attachments/assets/7ae3a848-40fa-4348-a65f-61c19584c206" />

**📝 Scenario:** Analyze a malicious executable (`VPN.exe`) using sandbox reports to uncover C2 communication, encryption mechanisms, persistence techniques, and data exfiltration behavior.  
**🗂️ Category:** Malware Analysis | Threat Intelligence  
**🎯 Tactics:** Execution, Persistence, Credential Access, Exfiltration, Defense Evasion  
**🛠️ Tools:** ANY.RUN Sandbox, MITRE ATT&CK Framework  
**⚡ Difficulty:** Medium | ⏱ ~45 mins

---

# 🧩 Analysis & Findings

---

## 🔹 **Q1 – Malware Creation Timestamp 🕐**
**Objective:** Determine the origin time of the malware sample.

**🔍 Analysis:**  
Inspected the sample's basic properties in the ANY.RUN report, focusing on compilation/creation timestamps.

<img width="975" height="470" alt="image" src="https://github.com/user-attachments/assets/7320f0ed-6d98-4a36-af7c-f41fc787ce40" />

**✅ Answer:**

<img width="975" height="243" alt="image" src="https://github.com/user-attachments/assets/aef28a70-3f3a-49ca-a27f-ee67bb702f30" />

`2022-09-28 17:40`

---

## 🔹 **Q2 – Command & Control (C2) Server 🕵️‍♂️**
**Objective:** Identify the primary C2 server used for communication.

**🔍 Analysis:**  

<img width="975" height="477" alt="image" src="https://github.com/user-attachments/assets/43aedcd3-e22d-41d6-a325-8c656aa97fdf" />

Reviewed network activity in the sandbox report, specifically HTTP POST requests to malicious domains.

**✅ Answer:**  

<img width="975" height="227" alt="image" src="https://github.com/user-attachments/assets/e88e5bd2-66cd-47ce-9cb6-18805ead82f5" />

`http://171.22.28.221/5c06c05b7b34e8e6.php`

---

## 🔹 **Q3 – Initial Post-Infection Request 📦**
**Objective:** Identify the first library fetched after execution.

**🔍 Analysis:**  

<img width="975" height="448" alt="image" src="https://github.com/user-attachments/assets/7c64c0e9-44a6-4c63-a1a0-3107362926d3" />

Examined HTTP GET requests in the "Network Communication" section of the activity summary.

**✅ Answer:**  

<img width="975" height="229" alt="image" src="https://github.com/user-attachments/assets/a42f7107-7fe9-482e-933d-3c751ecb407b" />

`sqlite3.dll`

---

## 🔹 **Q4 – RC4 Decryption Key 🔑**
**Objective:** Extract the RC4 key used to decode base64-encoded strings.

**🔍 Analysis:**  

<img width="975" height="469" alt="image" src="https://github.com/user-attachments/assets/e754fc5e-b0ab-4fbc-8d4f-d97b96d3c82c" />

Located the malware configuration section in the ANY.RUN report, revealing embedded keys.

**✅ Answer:**  

<img width="975" height="232" alt="image" src="https://github.com/user-attachments/assets/4a012ff3-b191-4fd1-b5ad-53d26fbe54a9" />

`5329514621441247975720749009`

---

## 🔹 **Q5 – Credential Theft Technique 🎯**
**Objective:** Identify the MITRE ATT&CK technique used for password stealing.

**🔍 Analysis:**  

<img width="975" height="472" alt="image" src="https://github.com/user-attachments/assets/7731da19-531b-44f7-bcb2-5660b031f44a" />

Correlated observed behaviors (reading stored credentials) with MITRE ATT&CK technique IDs.

**✅ Answer:**  

<img width="975" height="220" alt="image" src="https://github.com/user-attachments/assets/a9d7e1da-d6c8-4fdb-8b01-bf9230a6432e" />

`T1555` (Credentials from Password Stores)

---

## 🔹 **Q6 – Target Directory for DLL Deletion 🗑️**
**Objective:** Determine which directory the malware cleans up by deleting DLLs.

**🔍 Analysis:**  

<img width="975" height="467" alt="image" src="https://github.com/user-attachments/assets/18454b02-3c9f-4ee1-a338-b58532e1035d" />

Analyzed child process command lines, focusing on `del` operations.

**✅ Answer:**  

<img width="975" height="222" alt="image" src="https://github.com/user-attachments/assets/2fad9d09-4fa4-49e0-b472-4a495026b6fe" />

`C:\ProgramData`

---

## 🔹 **Q7 – Self-Deletion Delay ⏱️**
**Objective:** Find the time interval between data exfiltration and self-deletion.

**🔍 Analysis:**  

<img width="975" height="487" alt="image" src="https://github.com/user-attachments/assets/edf2a81b-5112-41b3-bb58-91ba84d3a243" />

Traced process timeline events and compared timestamps of exfiltration vs. deletion commands.

**✅ Answer:** 

<img width="975" height="294" alt="image" src="https://github.com/user-attachments/assets/e3585bcb-cb2e-4f7a-ad1d-72d08f461589" />

`5 seconds`

---

# 📊 IOCs & Key Findings

| Indicator Type | Value |
|----------------|-------|
| **Sample Hash (MD5)** | `fdaaf0697c0506c9a1a90974cf46e77f` |
| **C2 Server** | `http://171.22.28.221/5c06c05b7b34e8e6.php` |
| **RC4 Key** | `5329514621441247975720749009` |
| **Targeted Directory** | `C:\ProgramData` |
| **MITRE Technique** | `T1555` |
| **Creation Time** | `2022-09-28 17:40` |
| **Self-Deletion Delay** | `5 seconds` |

---

# 🧠 Behavior Summary

- **Initial Access:** Executed via command interpreter (`cmd.exe`) with embedded timeout and cleanup commands.
- **Persistence:** Uses scheduled tasks or startup entries (inferred from `%APPDATA%` references).
- **Credential Access:** Searches for password stores (browsers, system credentials) via `sqlite3.dll`.
- **C2 Communication:** HTTP POST to controlled PHP endpoint for data exfiltration.
- **Defense Evasion:** Deletes DLLs in `C:\ProgramData`, self-deletes after 2.5 seconds post-exfiltration.
- **Encryption:** Uses RC4 with hardcoded key to decrypt C2 configurations.

---

# 🛡️ MITRE ATT&CK Mapping

| Tactic | Technique |
|--------|-----------|
| **Execution** | T1059 (Command and Scripting Interpreter) |
| **Persistence** | T1547 (Boot or Logon Autostart Execution) |
| **Credential Access** | **T1555 (Credentials from Password Stores)** |
| **Exfiltration** | T1041 (Exfiltration Over C2 Channel) |
| **Defense Evasion** | T1070 (Indicator Removal on Host) |

---

### ✅ Lessons Learned:
- Malware often uses simple encryption (RC4) with static keys for string obfuscation.
- Timestamps in PE headers can reveal initial compilation times.
- Self-deletion scripts are commonly used post-exfiltration to reduce forensic footprints.
- Credential stealers frequently leverage SQLite libraries to access browser login databases.

---

**🔗 Lab Reference:** OSKI Malware Analysis – ANY.RUN Interactive Report  
**📂 Sample:** `VPN.exe` | **Threat Score:** 62/100
