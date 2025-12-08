
# 🟡 Amadey Malware – CyberDefenders Write-Up
                                                               # Memory Forensics – Malware Analysis


<img width="975" height="473" alt="image" src="https://github.com/user-attachments/assets/0745fc81-eb98-4ded-a6cb-dbc36cfe1036" />


## 📌 Introduction
In this challenge, we were provided with a memory dump from an infected machine suspected of running the Amadey malware in the CyberDefenders.org challenge. 

**Amadey Trojan**: A Windows-based information stealer and botnet first observed in 2018. It functions as a downloader for additional malware payloads while exfiltrating credentials and sensitive data through C2 communications. Known for persistence mechanisms including scheduled tasks and registry modifications.

Amadey is a modular malware variant primarily used for stealing sensitive information and conducting various malicious activities, including credential theft and the distribution of additional payloads. Known for its persistence mechanisms and ability to evade detection, Amadey poses a significant threat to infected systems.

After completing the challenge, I noticed the absence of a detailed write-up for this lab. Therefore, I decided to document my analysis to help others enhance their digital forensic skills and apply professional methodologies in memory forensics.

## 🛠️ Environment & Methodology
**Environment Setup:**  
The analysis was conducted in a controlled lab environment with Volatility 3 as the primary tool for memory forensics.

**Methodology:**  
Before addressing specific challenge questions, I focused on understanding the memory image through systematic information gathering using Volatility 3.

### 1. Understanding the Operating System
Determining the OS is crucial as it influences the analysis approach. Using the `windows.info` plugin:

```bash
python3 vol.py -f /home/ubuntu/Desktop/Start\ here/Artifacts/Windows\ 7\ x64-Snapshot4.vmem windows.info
```

**Output:** We are working with a 64-bit Windows 7 SP1 system.

### 2. Collecting Basic Information
After OS identification, I examined running processes using `windows.pstree` to identify suspicious parent-child relationships:

```bash
python3 vol.py -f /home/ubuntu/Desktop/Start\ here/Artifacts/Windows\ 7\ x64-Snapshot4.vmem windows.pstree
```

**Finding:** A suspicious process named **"lssass.exe"** (masquerading as legitimate lsass.exe) appeared without proper parent linkage and spawned **rundll32.exe** as a child process—a known Amadey behavior pattern.

### 3. Analyzing Processes
I used multiple Volatility plugins to investigate hidden processes, loaded DLLs, command lines, and network connections:

**Hidden Processes & DLLs:**
```bash
python3 vol.py -f /home/ubuntu/Desktop/Start\ here/Artifacts/Windows\ 7\ x64-Snapshot4.vmem windows.psscan | grep -i 2748
python3 vol.py -f /home/ubuntu/Desktop/Start\ here/Artifacts/Windows\ 7\ x64-Snapshot4.vmem windows.dlllist | grep -i 2748
python3 vol.py -f /home/ubuntu/Desktop/Start\ here/Artifacts/Windows\ 7\ x64-Snapshot4.vmem windows.dlllist | grep -i 3064
```

**Command Line Analysis:**
```bash
python3 vol.py -f /home/ubuntu/Desktop/Start\ here/Artifacts/Windows\ 7\ x64-Snapshot4.vmem windows.cmdline | grep -i 2748
python3 vol.py -f /home/ubuntu/Desktop/Start\ here/Artifacts/Windows\ 7\ x64-Snapshot4.vmem windows.cmdline | grep -i 3064
```

**Network Connections:**  
Analysis revealed outbound connections to **41.75.84.12** (Nigeria-based C2) on port 80.

**File System Artifacts:**  
```bash
python3 vol.py -f /home/ubuntu/Desktop/Start\ here/Artifacts/Windows\ 7\ x64-Snapshot4.vmem windows.filescan | grep -i lssass
```

---

## ✔️ Questions & Answers
**Q1 — Malicious Parent Process**  
Method: Identified through windows.pstree and windows.psscan as a rogue process masquerading as legitimate lsass.exe.

<img width="975" height="370" alt="image" src="https://github.com/user-attachments/assets/461d420f-bc73-4ecb-9479-0f4306636280" />


<img width="975" height="445" alt="image" src="https://github.com/user-attachments/assets/184e4e48-48ed-4d47-8585-24abca3c4e37" />


<img width="975" height="465" alt="image" src="https://github.com/user-attachments/assets/f2f859c3-b285-484f-b56d-fd95795ac1be" />


Answer: lssass.exe  


<img width="975" height="240" alt="image" src="https://github.com/user-attachments/assets/55abef1f-12f8-4f15-a87d-23f721ab261d" />


**Q2 — Malware Process Location**  

Method: Located via windows.dlllist and windows.filescan.


<img width="975" height="111" alt="image" src="https://github.com/user-attachments/assets/ff201499-2d1e-4d3e-9e0e-3c224fcb16c4" />


Answer: C:\Users\0XSH3R~1\AppData\Local\Temp\925e7e99c5\lssass.exe  

<img width="975" height="240" alt="image" src="https://github.com/user-attachments/assets/29daf79e-1696-47c5-9fc2-cfc5bef51f53" />

**Q3 — Command & Control (C2) Server IP**  

Method: Extracted from network connection artifacts within memory.


<img width="975" height="87" alt="image" src="https://github.com/user-attachments/assets/aab0524d-f985-4b6b-93d1-5d105e421d91" />


Answer: 41.75.84.12  

<img width="975" height="223" alt="image" src="https://github.com/user-attachments/assets/ae6a9a51-0ef6-41c5-9630-c0796b6322d0" />

**Q4 — Number of Files Downloaded**  
Method: Determined by analyzing malware behavior and dropped file artifacts.
Answer: 2 

<img width="975" height="219" alt="image" src="https://github.com/user-attachments/assets/a29e3507-4ee9-4faa-88a0-8dce28a5939d" />

**Q5 — Downloaded Payload Path**  

Method: Found via windows.cmdline and filescan.

<img width="975" height="123" alt="image" src="https://github.com/user-attachments/assets/55e25feb-96d5-4eb3-855d-4497f9a2ff59" />

Answer: C:\Users\0xSh3rl0ck\AppData\Roaming\116711e5a2ab05\clip64.dll  

<img width="975" height="226" alt="image" src="https://github.com/user-attachments/assets/5e73e6df-8750-4b05-8faa-d22cc7abafd7" />

**Q6 — Child Process Executing Payload**  

Method: Identified as child process (PID 3064) of lssass.exe via windows.pstree.

<img width="975" height="265" alt="image" src="https://github.com/user-attachments/assets/57fbe158-ab6b-4b34-873f-f43833278c59" />


Answer: rundll32.exe  

<img width="975" height="222" alt="image" src="https://github.com/user-attachments/assets/6c0a8e7f-fce8-4a91-8ad2-941c78c3ad29" />

**Q7 — Additional Persistence Location**  

Method: Discovered using windows.filescan searching for scheduled tasks.


<img width="975" height="78" alt="image" src="https://github.com/user-attachments/assets/f2310508-af52-4daa-9c28-99dc394dfff3" />


Answer: C:\Windows\System32\Tasks\lssass.exe  

<img width="975" height="268" alt="image" src="https://github.com/user-attachments/assets/bb732c1b-08ad-4597-bdee-45414c2576df" />

---

## 📎 References
CyberDefenders Lab: Amadey  
Volatility3 Documentation



**Tactics, Techniques & Procedures (TTPs):**
- T1036: Masquerading (lssass.exe vs lsass.exe)
- T1055: Process Injection
- T1071: Application Layer Protocol
- T1053: Scheduled Task
- T1105: Ingress Tool Transfer

## 🔗 References
CyberDefenders Lab: Amadey Challenge  
MITRE ATT&CK: Techniques T1036, T1055, T1071, T1053, T1105

## 📝 Analyst Notes
- The malware uses WOW64 to run 32-bit process on 64-bit system
- Temporary directory naming uses hex subfolders for evasion
- Rundll32.exe is leveraged for DLL execution, a common LOLBin
- Scheduled task ensures persistence beyond reboot

This write-up provides a comprehensive forensic breakdown of Amadey Trojan behavior, documenting both the analytical methodology and specific IOCs. Ideal for SOC analysts and DFIR teams to quickly identify and respond to similar infections.🔥
