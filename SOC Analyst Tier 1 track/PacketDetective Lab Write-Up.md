# PacketDetective Lab Write-Up: Network Traffic Analysis & Attack Reconstruction

## **Lab Overview**
- **Platform**: CyberDefenders
- **Lab Name**: PacketDetective
- **Category**: Network Forensics
- **Tactics**: Execution, Defense Evasion, Command and Control
- **Tools Used**: Wireshark
- **Difficulty**: Easy
- **Scenario**: In September 2020, your SOC detected suspicious activity from a user device, flagged by unusual SMB protocol usage. Initial analysis indicates a possible compromise of a privileged account and remote access tool usage by an attacker. Your task is to examine network traffic in the provided PCAP files to identify key indicators of compromise (IOCs) and gain insights into the attacker's methods, persistence tactics, and goals. Construct a timeline to better understand the progression of the attack.

---

## **Environment & Artifacts**
The investigation is based on three PCAP files:
- `Traffic-1.pcapng`
- `Traffic-2.pcapng`
- `Traffic-3.pcapng`

All analysis was performed using **Wireshark** on a forensics workstation.

---

## **Analysis of Traffic-1.pcapng**

### **1. SMB Traffic Volume**
**Objective**: Determine the total number of bytes of the SMB protocol.

**Method**:
- Opened `Traffic-1.pcapng` in Wireshark.
- Navigated to **Statistics → Protocol Hierarchy**.
- Located the SMB protocol entry.

**Finding**:
- Total SMB bytes: **4406 bytes**.
- This indicates a relatively small amount of SMB traffic, likely related to specific actions like authentication or file access.

> *Figure 1: Wireshark Protocol Hierarchy showing SMB byte count.*

---

### **2. SMB Authentication Username**
**Objective**: Identify the username used for SMB authentication.

**Method**:
- Applied display filter: `smb2` or `smb` to isolate SMB traffic.
- Examined SMB Session Setup requests.

**Finding**:
- The username **`Administrator`** was used for authentication.
- This suggests the attacker may have compromised a privileged account.

> *Figure 2: SMB packet showing username "Administrator".*

---

### **3. Files Accessed via SMB**
**Objective**: Determine which files the attacker accessed or manipulated over SMB.

**Method**:
- Filtered for SMB traffic and examined file operation commands (e.g., `Create Request`, `Read`, `Write`).
- Looked for accessed paths.

**Finding**:
- The attacker accessed the **`eventlog`** file (Windows Event Logs).
- This is a strong indicator of defense evasion – the attacker likely cleared or tampered with logs to cover tracks.

> *Figure 3: SMB Create Request for eventlog file.*

---

### **4. Log Clearing Timestamp**
**Objective**: Establish the exact time when the event log was accessed/cleared.

**Method**:
- Located the packet containing the SMB operation on `eventlog`.
- Noted the packet timestamp.

**Finding**:
- The log clearing activity occurred at **2020-09-23 16:50** (UTC or local time as per PCAP).
- This timestamp becomes a key point in the attack timeline.

> *Figure 4: Packet timestamp showing log clearing time.*

---

## **Analysis of Traffic-2.pcapng**

### **5. Named Pipe Communication (RPC)**
**Objective**: Identify the service that communicated using a named pipe, indicating lateral movement via RPC.

**Method**:
- Applied display filter: `dcerpc` to isolate DCE/RPC traffic.
- Followed the TCP stream of the first DCERPC packet to examine the bind and request details.

**Finding**:
- The traffic revealed a named pipe communication.
- The service name found in the stream was **`atsvc`** (Task Scheduler service).
- The `atsvc` service is often used for remote task scheduling – a common lateral movement technique.

> *Figure 5: DCERPC stream showing named pipe reference.*
> *Figure 6: Service identification as "atsvc".*

---

### **6. Communication Duration**
**Objective**: Measure how long the suspicious communication lasted between the two hosts (172.16.66.1 and 172.16.66.36).

**Method**:
- Used **Statistics → Conversations** and selected the IPv4 tab.
- Located the conversation between `172.16.66.1` and `172.16.66.36`.
- Noted the duration column.

**Finding**:
- The conversation duration was **11.7247 seconds**.
- This brief but purposeful communication suggests a specific remote task execution or data exchange.

> *Figure 7: Conversations tab showing duration.*

---

## **Analysis of Traffic-3.pcapng**

### **7. Suspicious Username for Persistence**
**Objective**: Identify the non‑standard username used to set up persistent access.

**Method**:
- Added the **Username** column to the packet list view (Edit → Preferences → Columns → Add `smb2.ntlmssp.auth.username` or similar).
- Scanned for any username appearing in SMB authentication packets.

**Finding**:
- The only username observed in this traffic was the one used for persistence (the specific value was highlighted in the user's notes; we’ll denote it as **`[REDACTED]`** or simply the discovered username – the user didn't specify, but likely something like a custom account). In the notes, it's implied that this username is the only one present, so it's the persistence account.
- *Note: The exact username wasn't extracted from the notes, but in a real write-up we would provide it. For the sake of this write-up, we'll note that it was a non‑standard account (e.g., not Administrator).*

> *Figure 8: Username column showing the suspicious account.*

---

### **8. Remote Execution Executable**
**Objective**: Determine the executable file used for remote process execution.

**Method**:
- Used **File → Export Objects → SMB** to list all files transferred over SMB.
- Reviewed the list for executable files (`.exe`).

**Finding**:
- The only executable file present was **`PSEXESVC.exe`**.
- This is the service binary for **PsExec**, a legitimate Microsoft tool often abused by attackers for remote execution.
- The presence of this file confirms the attacker used PsExec to run commands remotely on the compromised system.

> *Figure 9: Export SMB objects dialog showing PSEXESVC.exe.*
> *Figure 10: PSEXESVC.exe details.*

---

## **Indicators of Compromise (IOCs)**

| Type | Value |
|------|-------|
| **Victim IPs** | 172.16.66.1, 172.16.66.36 |
| **SMB Authentication User** | Administrator |
| **Persistence Username** | [Non‑standard username discovered in Traffic‑3] |
| **File Accessed (Logs)** | eventlog |
| **Log Clearing Time** | 2020-09-23 16:50 |
| **RPC Service** | atsvc |
| **Remote Execution Tool** | PSEXESVC.exe |
| **Communication Duration** | 11.7247 seconds |

---

## **Attack Timeline**

| Timestamp | Event |
|-----------|-------|
| 2020-09-23 16:50 | Attacker accesses/clears event logs via SMB (Traffic‑1) |
| Shortly after | RPC communication via named pipe using `atsvc` service (Traffic‑2) |
| Concurrently | Suspicious username used to establish persistence (Traffic‑3) |
| During same window | PsExec service binary (`PSEXESVC.exe`) transferred and executed remotely |

---

## **MITRE ATT&CK Mapping**

| Tactic | Technique | ID |
|--------|-----------|----|
| **Initial Access** | Valid Accounts (Domain/Admin) | T1078 |
| **Execution** | Remote Services: PsExec | T1569.002 |
| **Persistence** | Create Account | T1136 |
| **Defense Evasion** | Indicator Removal: Clear Event Logs | T1070.001 |
| **Lateral Movement** | Remote Services: SMB/Admin Shares | T1021.002 |
| **Command and Control** | Remote Access Tools | T1219 |

---

## **Conclusion**

The investigation of the three PCAP files reveals a multi‑stage attack where an adversary:
1. Gained access using the **Administrator** account.
2. Immediately cleared event logs (`eventlog`) at **2020-09-23 16:50** to evade detection.
3. Used **RPC over named pipes** (`atsvc`) for lateral movement and task scheduling.
4. Established persistence with a **non‑standard username**.
5. Deployed **PsExec (`PSEXESVC.exe`)** for remote command execution.

These findings provide a clear timeline and set of IOCs that can be used for threat hunting and future detection. The attacker's goal, as per the scenario, was likely to maintain covert access and potentially exfiltrate data, though data exfiltration was not directly observed in these PCAPs.

---

## **Tools Used**
- **Wireshark**: PCAP analysis, filtering, protocol hierarchy, object export.
- **Columns and Statistics features** for timeline and conversation analysis.

---

## **References**
- CyberDefenders.org – PacketDetective Lab
- MITRE ATT&CK® Framework
- PsExec documentation

---
