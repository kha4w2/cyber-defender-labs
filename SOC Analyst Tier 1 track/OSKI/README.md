# 🔍 OSKI Malware Analysis – Threat Hunting Lab

## 📌 Lab Overview

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

**✅ Answer:**  
`2022-09-28 17:40`

---

## 🔹 **Q2 – Command & Control (C2) Server 🕵️‍♂️**
**Objective:** Identify the primary C2 server used for communication.

**🔍 Analysis:**  
Reviewed network activity in the sandbox report, specifically HTTP POST requests to malicious domains.

**✅ Answer:**  
`http://171.22.28.221/5c06c05b7b34e8e6.php`

---

## 🔹 **Q3 – Initial Post-Infection Request 📦**
**Objective:** Identify the first library fetched after execution.

**🔍 Analysis:**  
Examined HTTP GET requests in the "Network Communication" section of the activity summary.

**✅ Answer:**  
`sqlite3.dll`

---

## 🔹 **Q4 – RC4 Decryption Key 🔑**
**Objective:** Extract the RC4 key used to decode base64-encoded strings.

**🔍 Analysis:**  
Located the malware configuration section in the ANY.RUN report, revealing embedded keys.

**✅ Answer:**  
`5329514621441247975720749009`

---

## 🔹 **Q5 – Credential Theft Technique 🎯**
**Objective:** Identify the MITRE ATT&CK technique used for password stealing.

**🔍 Analysis:**  
Correlated observed behaviors (reading stored credentials) with MITRE ATT&CK technique IDs.

**✅ Answer:**  
`T1555` (Credentials from Password Stores)

---

## 🔹 **Q6 – Target Directory for DLL Deletion 🗑️**
**Objective:** Determine which directory the malware cleans up by deleting DLLs.

**🔍 Analysis:**  
Analyzed child process command lines, focusing on `del` operations.

**✅ Answer:**  
`C:\ProgramData`

---

## 🔹 **Q7 – Self-Deletion Delay ⏱️**
**Objective:** Find the time interval between data exfiltration and self-deletion.

**🔍 Analysis:**  
Traced process timeline events and compared timestamps of exfiltration vs. deletion commands.

**✅ Answer:**  
`2.5 seconds`

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
| **Self-Deletion Delay** | `2.5 seconds` |

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

## 📸 Evidence Snapshots
*(Refer to uploaded screenshots for detailed views of network activity, process trees, and configuration extracts.)*

---

### ✅ Lessons Learned:
- Malware often uses simple encryption (RC4) with static keys for string obfuscation.
- Timestamps in PE headers can reveal initial compilation times.
- Self-deletion scripts are commonly used post-exfiltration to reduce forensic footprints.
- Credential stealers frequently leverage SQLite libraries to access browser login databases.

---

**🔗 Lab Reference:** OSKI Malware Analysis – ANY.RUN Interactive Report  
**📂 Sample:** `VPN.exe` | **Threat Score:** 62/100
