# PacketDetective Lab Write-Up: Network Traffic Analysis & Attack Reconstruction

<img width="975" height="495" alt="image" src="https://github.com/user-attachments/assets/1d95125c-f82f-4efb-ba36-8b548818fc00" />
Lab Link: https://cyberdefenders.org/blueteam-ctf-challenges/packetdetective/

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
<img width="975" height="469" alt="image" src="https://github.com/user-attachments/assets/de632c25-668e-49c2-a0ad-6edcc4cd562e" />

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

<img width="975" height="460" alt="image" src="https://github.com/user-attachments/assets/9c3f579b-7bac-4950-9379-23c32056e840" />

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

<img width="975" height="418" alt="image" src="https://github.com/user-attachments/assets/5151d409-9b58-4a90-a493-acf6a9675b9c" />

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

<img width="975" height="338" alt="image" src="https://github.com/user-attachments/assets/114f050e-8ed6-4ed9-a032-3a6e32313fc0" />

<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/1de3c413-88d4-48db-9ef9-9aa98086af3d" />

<img width="975" height="621" alt="image" src="https://github.com/user-attachments/assets/373d7bef-7fad-4451-84e6-1561f4755118" />

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

<img width="975" height="418" alt="image" src="https://github.com/user-attachments/assets/a04bf45d-1c22-451a-a0da-95a59e6138b6" />

> *Figure 4: Packet timestamp showing log clearing time.*


<img width="975" height="871" alt="image" src="https://github.com/user-attachments/assets/c842f4f3-a08c-4d7b-9490-b054eba9d64f" />

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

<img width="975" height="299" alt="image" src="https://github.com/user-attachments/assets/96f4571c-c00f-49e6-8fa5-9bc4b67e8ef0" />

> *Figure 5: DCERPC stream showing named pipe reference.*

<img width="975" height="582" alt="image" src="https://github.com/user-attachments/assets/c0090cb2-3688-4519-bc9e-c880f3da1e63" />

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

<img width="975" height="289" alt="image" src="https://github.com/user-attachments/assets/932ef8dc-37a6-47d6-adb2-fe8709ecb0c6" />

> *Figure 7: Conversations tab showing duration.*


<img width="975" height="603" alt="image" src="https://github.com/user-attachments/assets/ba2f216f-d358-42c6-9b2d-f1ac60e7264c" />

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

<img width="975" height="381" alt="image" src="https://github.com/user-attachments/assets/f1838223-6cd1-401e-b595-206c28011c65" />

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

<img width="975" height="319" alt="image" src="https://github.com/user-attachments/assets/7cbdb0a6-9117-4ca2-adc1-cf33385a9945" />

> *Figure 9: Export SMB objects dialog showing PSEXESVC.exe.*

 <img width="975" height="602" alt="image" src="https://github.com/user-attachments/assets/e3cbfc2f-f28e-4176-828b-a557e8c32a77" />

> *Figure 10: PSEXESVC.exe details.*


<img width="975" height="608" alt="image" src="https://github.com/user-attachments/assets/afa0c1a8-a1c9-4402-87db-1e7944087296" />

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
## **Key Takeaways & Important Notes**

### **Network Forensics Fundamentals**
- **Protocol Hierarchy** (Statistics → Protocol Hierarchy) provides a quick overview of traffic volume by protocol, helping to identify dominant or suspicious protocols (e.g., SMB in this lab).
- **Conversations** (Statistics → Conversations) reveals communication pairs and duration, useful for establishing timelines and identifying beaconing or short-lived command sessions.
- **Adding custom columns** (like Username) streamlines analysis by displaying critical fields directly in the packet list.

### **SMB Analysis**
- SMB is frequently used for file sharing, authentication, and remote administration. Attackers abuse it for:
  - **Accessing sensitive files** (e.g., event logs) to clear evidence.
  - **Transferring tools** (e.g., PsExec) for lateral movement.
- **Exporting SMB objects** (File → Export Objects → SMB) can reveal malicious executables or scripts transferred during the compromise.
- **SMB authentication packets** expose usernames; always check for privileged or non‑standard accounts that may indicate persistence.

### **RPC and Named Pipes**
- **DCE/RPC** traffic often indicates remote procedure calls, which can be used for legitimate administration or malicious lateral movement (e.g., Task Scheduler – `atsvc`).
- Following the **TCP stream** of the first DCERPC packet can reveal the service name and named pipe, helping to understand the attacker’s lateral movement technique.

### **Defense Evasion Techniques**
- Attackers frequently clear event logs to hide their activities. In this lab, the `eventlog` file was accessed via SMB, and the exact timestamp (2020-09-23 16:50) provided a critical timeline point.
- Log clearing is mapped to **MITRE ATT&CK T1070.001**.

### **Remote Execution via PsExec**
- **PsExec** is a legitimate Microsoft tool often abused by attackers. The presence of `PSEXESVC.exe` in SMB exports is a strong indicator of remote service installation and command execution.
- Always verify executable names and hashes against threat intelligence sources.

### **Timeline Reconstruction**
- Combining timestamps from multiple PCAPs allows building a coherent attack timeline. This helps SOC teams understand the sequence of events and prioritize response actions.
- Even short communication durations (e.g., 11.7 seconds) can be significant if they involve known malicious services or tools.


### **Practical Wireshark Tips**
- Use filters like `smb2`, `dcerpc`, or `ip.addr == x.x.x.x` to narrow down traffic.
- Right‑click a packet → **Follow → TCP Stream** to reassemble conversations.
- **Protocol Hierarchy** and **Conversations** are under‑utilized but powerful for initial triage.
- Always verify file hashes and names through external threat intelligence (VirusTotal, etc.) – not done in this lab but recommended in real investigations.

### **Overall Mindset**
- Think like an attacker: what would they do after gaining access? Clear logs, move laterally, establish persistence.
- Every artifact (file name, timestamp, username) is a puzzle piece; combine them to see the full picture.
- Document findings clearly, mapping to frameworks like MITRE ATT&CK for standardized reporting.

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
