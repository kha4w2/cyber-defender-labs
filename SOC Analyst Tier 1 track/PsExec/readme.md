# **🔎 PsExec Hunt Lab — CyberDefenders Write-up**

 Lab Link: https://cyberdefenders.org/blueteam-ctf-challenges/psexec-hunt/
 

<img width="1916" height="903" alt="image" src="https://github.com/user-attachments/assets/54821377-8b0b-4adb-9774-7f5cde82a463" />


---

## 🧠 **Lab Overview**  
**Platform:** CyberDefenders  
**Lab Name:** PsExec Hunt  
**Category:** Network Forensics  
**Difficulty:** Easy  
**Duration:** 30 minutes  
**Tools Used:** Wireshark  
**MITRE ATT&CK Tactics:** Execution, Defense Evasion, Discovery, Lateral Movement  

**Objective:**  
Analyze a PCAP file containing SMB traffic to investigate suspicious lateral movement using PsExec. Identify the attacker's entry point, compromised systems, credentials used, and the techniques employed to move across the network.

---

## 📜 **Scenario**  
An Intrusion Detection System (IDS) alert flagged suspicious lateral movement activity involving PsExec within the network. As a SOC Analyst, you are tasked with analyzing the provided network capture (PCAP) to trace the attacker's activities, determine the initial access point, identify compromised machines, and uncover the methods used for lateral movement.

---

## 🧩 **Key Concepts Explained**

### 🛡️ **IDS (Intrusion Detection System)**  
An IDS monitors network or host activities for malicious actions or policy violations. It uses signatures or anomalies to generate alerts but does not block traffic — that’s the role of an IPS (Intrusion Prevention System).

### 🔄 **Lateral Movement**  
A technique used by attackers after gaining initial access to move laterally across a network, compromising additional systems to reach high-value assets, escalate privileges, or maintain persistence.

### ⚙️ **PsExec**  
A legitimate Sysinternals tool that allows remote command execution on Windows systems via Windows services. It is commonly abused by attackers for lateral movement because it can run commands remotely using administrative shares (`ADMIN$`, `IPC$`).

---

## 🕵️ **Investigation Steps & Answers**

### **❓ Q1: Initial Attacker IP Address**
> *To effectively trace the attacker's activities within our network, can you identify the IP address of the machine from which the attacker initially gained access?*

**🔍 Approach:**  
Opened the PCAP in Wireshark and navigated to `Statistics → Conversations`. Noticed that IP `10.0.0.130` had a significantly higher packet count (38,284) compared to others, indicating it was the source of malicious activity.

<img width="975" height="517" alt="image" src="https://github.com/user-attachments/assets/d9fcfebe-eaa4-4ee8-b55a-2f909f1db1f7" />


<img width="975" height="224" alt="image" src="https://github.com/user-attachments/assets/cdd29a64-8d36-485e-ab5b-a3e90cc0139f" />


**✅ Answer:**  
`10.0.0.130`

---

### **❓ Q2: First Pivoted Hostname**
> *To fully understand the extent of the breach, can you determine the machine's hostname to which the attacker first pivoted?*

**🔍 Approach:**  
Followed TCP streams involving `10.0.0.130`. In the SMB traffic, the Server Name field clearly displayed `Sales-PC`.


<img width="975" height="507" alt="image" src="https://github.com/user-attachments/assets/6cfdb554-1e67-427b-b099-efc07936f3b2" />


<img width="975" height="496" alt="image" src="https://github.com/user-attachments/assets/b2331507-c41f-468e-808c-660476e958a6" />


<img width="975" height="231" alt="image" src="https://github.com/user-attachments/assets/01d98ff3-0873-4b47-8cb1-34ef8487af96" />


**✅ Answer:**  
`Sales-PC`

---

### **❓ Q3: Attacker’s Username**
> *Knowing the username of the account the attacker used for authentication will give us insights into the extent of the breach. What is the username utilized by the attacker for authentication?*

**🔍 Approach:**  
Filtered for `smb2` packets and examined the `Session Setup Request`. Found the field: `User: \ssales`.


<img width="975" height="511" alt="image" src="https://github.com/user-attachments/assets/c825f116-0bf9-4829-a4e1-bc7871e2e60d" />


<img width="975" height="258" alt="image" src="https://github.com/user-attachments/assets/f31f852e-ad1e-45b3-b653-8eafb0ffb9f1" />


**✅ Answer:**  
`ssales`

---

### **❓ Q4: Service Executable Name**
> *After figuring out how the attacker moved within our network, we need to know what they did on the target machine. What's the name of the service executable the attacker set up on the target?*

**🔍 Approach:**  
Searched for DCERPC/SVCCTL traffic related to service creation. Located a packet where the service name `PSEXESVC` was created remotely.


<img width="975" height="508" alt="image" src="https://github.com/user-attachments/assets/1a0e1bc1-b86a-436f-94e1-1de478481ba2" />



<img width="975" height="238" alt="image" src="https://github.com/user-attachments/assets/20d02b20-ca64-4774-ad14-70e1d3746b53" />


**✅ Answer:**  
`PSEXESVC`

---

### **❓ Q5: Share Used for Installation**
> *We need to know how the attacker installed the service on the compromised machine to understand the attacker's lateral movement tactics. This can help identify other affected systems. Which network share was used by PsExec to install the service on the target machine?*

**🔍 Approach:**  
PsExec typically copies the service executable via the `ADMIN$` share. Verified by checking SMB `Tree Connect` requests to `\\Sales-PC\ADMIN$`.


<img width="975" height="525" alt="image" src="https://github.com/user-attachments/assets/0e60a816-fd98-4477-8e4c-cdf335dedbe7" />


<img width="693" height="181" alt="image" src="https://github.com/user-attachments/assets/3e0f1452-943e-4973-8568-e12c3f91baa3" />


**✅ Answer:**  
`ADMIN$`

---

### **❓ Q6: Share Used for Communication**
> *We must identify the network share used to communicate between the two machines. Which network share did PsExec use for communication?*

**🔍 Approach:**  
PsExec uses `IPC$` for named pipe communication. Observed SMB connections to `\\Sales-PC\IPC$` following service installation.

<img width="687" height="152" alt="image" src="https://github.com/user-attachments/assets/71ddb88b-a90a-4dbc-b123-651fe82944ad" />

**✅ Answer:**  
`IPC$`

---

### **❓ Q7: Second Pivoted Hostname**
> *Now that we have a clearer picture of the attacker's activities on the compromised machine, it's important to identify any further lateral movement. What is the hostname of the second machine the attacker targeted to pivot within our network?*

**🔍 Approach:**  
Reviewed later TCP streams and SMB traffic, finding references to another host: `Marketing-PC`.


<img width="975" height="479" alt="image" src="https://github.com/user-attachments/assets/31f65931-ada8-46c0-93cd-946316742e0b" />


<img width="975" height="510" alt="image" src="https://github.com/user-attachments/assets/cd6c3b23-fc83-4f2a-92bd-e70d0637fd97" />


<img width="975" height="491" alt="image" src="https://github.com/user-attachments/assets/9dcab10e-bf33-4759-977c-eac603d570dd" />


<img width="975" height="458" alt="image" src="https://github.com/user-attachments/assets/1a04964f-44d0-496e-ae52-2a119e299ac6" />


<img width="975" height="311" alt="image" src="https://github.com/user-attachments/assets/5728c988-873c-4665-b3db-cbc88fc481d4" />


**✅ Answer:**  
`Marketing-PC`

---

## 🎯 **Attack Chain Reconstruction**

```
Attacker Machine: 10.0.0.130
    ↓
First Pivot: Sales-PC (via SMB, user: ssales)
    ↓
Service Installed: PSEXESVC (via ADMIN$)
    ↓
Communication Channel: IPC$
    ↓
Second Pivot Attempt: Marketing-PC
```

---

## 📌 **Conclusion**
This lab provided hands-on experience in analyzing SMB/DCERPC traffic to detect PsExec-based lateral movement. Key takeaways include:

- Identifying anomalous source IPs through packet count analysis.
- Extracting hostnames and usernames from SMB session data.
- Recognizing PsExec artifacts: `PSEXESVC` service, `ADMIN$` for installation, `IPC$` for communication.
- Tracing multi-stage lateral movement across network hosts.

These skills are directly applicable to real-world SOC and DFIR investigations where rapid analysis of network captures is crucial for containing breaches.

--- 

🔹 *Write-up by* [Khaled Elgohary]  
🔹 *Platform:* CyberDefenders  
🔹 *Lab:* PsExec Hunt — Blue Team CTF Challenge
