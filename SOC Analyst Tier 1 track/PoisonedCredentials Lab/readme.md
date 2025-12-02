# 🛡️ PoisonedCredentials – LLMNR/NBT-NS Poisoning Analysis 


<img width="975" height="473" alt="image" src="https://github.com/user-attachments/assets/39b0a321-74b9-4d42-86e0-6a0aaa65fed6" />


## 📌 Lab Overview

**📝 Scenario:** Analyze network traffic to detect LLMNR/NBT-NS poisoning attacks, identify the rogue machine, compromised accounts, and affected systems.  
**🗂️ Category:** Network Forensics  
**🎯 Attack Techniques:** LLMNR/NBT-NS Poisoning, SMB Relay, Credential Theft  
**🛠️ Tools:** Wireshark  
**⚡ Difficulty:** Easy | ⏱ ~30 mins  

---

## 🧩 Analysis & Findings

### 🔹 Q1 – Identify Mistyped Query

**Objective:** Find the mistyped share name queried by `192.168.232.162`.  
**🔍 Analysis:** Applied Wireshark filter `llmnr and ip.src==192.168.232.162` to isolate the misspelled LLMNR query.  

<img width="975" height="438" alt="image" src="https://github.com/user-attachments/assets/71f97437-1969-4195-a187-0d0b88f7971b" />

<img width="975" height="469" alt="image" src="https://github.com/user-attachments/assets/aaa9a99d-bf43-448b-a0b7-3de72985ac8e" />

**✅ Answer:** `fileshaare`

<img width="975" height="322" alt="image" src="https://github.com/user-attachments/assets/b65a00e3-9d97-4e68-a82e-8a0d797c34f1" />



### 🔹 Q2 – Rogue Machine IP

**Objective:** Determine the IP address of the attacker's machine.  
**🔍 Analysis:** Located the LLMNR response packet sent immediately after the mistyped query, identifying the spoofing source.  

<img width="975" height="468" alt="image" src="https://github.com/user-attachments/assets/1025a10e-0044-41fd-862c-e24ab1e8006a" />

**✅ Answer:** `192.168.232.215`

<img width="975" height="244" alt="image" src="https://github.com/user-attachments/assets/7c2ea417-70dd-4b50-a75f-7a24c0cc6779" />

### 🔹 Q3 – Second Affected Machine
**Objective:** Identify the second victim that received poisoned responses.  
**🔍 Analysis:** Filtered by attacker IP (`ip.addr==192.168.232.215`) and examined unique communication partners.  
<img width="975" height="467" alt="image" src="https://github.com/user-attachments/assets/df37be35-39ff-4cd8-b85c-44fc61e9d44c" />

<img width="975" height="222" alt="image" src="https://github.com/user-attachments/assets/58ba144c-c315-4953-947a-3269cdfdc1fc" />

**✅ Answer:** `192.168.232.176`

### 🔹 Q4 – Compromised Username
**Objective:** Find the username of the compromised account.  
**🔍 Analysis:** Filtered SMB2 traffic involving the attacker (`smb2 and ip.addr==192.168.232.215`) and extracted credentials from NTLMSSP_AUTH.

<img width="975" height="463" alt="image" src="https://github.com/user-attachments/assets/f66963b6-7b89-4167-a717-8d425d0c9578" />

**✅ Answer:** `janesmith`

<img width="975" height="246" alt="image" src="https://github.com/user-attachments/assets/2df3a34e-30aa-4fe5-baa0-1941e8b9da90" />


### 🔹 Q5 – Accessed Hostname
**Objective:** Discover the hostname of the machine accessed via SMB.  
**🔍 Analysis:** Followed the TCP stream of the SMB session and inspected the `Session Setup` request for the target server name.  

<img width="975" height="453" alt="image" src="https://github.com/user-attachments/assets/b118d004-244c-4b1b-b5b2-01e21f5d3d38" />

**✅ Answer:** `AccountingPC`


<img width="975" height="247" alt="image" src="https://github.com/user-attachments/assets/0be7e8bb-aa09-4105-8d28-c46be12cdaa7" />

---

## 📊 Key Findings Summary
| Finding | Value |
|---------|-------|
| Mistyped Query | `fileshaare` |
| Rogue Machine IP | `192.168.232.215` |
| Second Victim IP | `192.168.232.176` |
| Compromised Username | `janesmith` |
| Accessed Hostname | `AccountingPC` |

---

## 🧠 Attack Chain Reconstruction
1. **Initial Query:** User at `192.168.232.162` mistyped "fileshare" as `fileshaare`, triggering LLMNR broadcast.
2. **Poisoning:** Attacker at `192.168.232.215` responded, claiming to be the requested resource.
3. **Credential Interception:** Victim sent NetNTLMv2 hash for user `janesmith` to the attacker.
4. **SMB Access:** Attacker used stolen credentials to authenticate to `AccountingPC` via SMB.
5. **Lateral Movement:** Attacker also poisoned responses for `192.168.232.176`, indicating attempted lateral movement.

---

## ✅ Mitigation Recommendations
- Disable LLMNR and NBT-NS protocols in network environments where they are not essential.
- Enforce strong network authentication controls such as Kerberos and SMB signing.
- Monitor network traffic for unexpected LLMNR/NBT-NS responses using IDS/IPS rules.
- Implement network segmentation to limit broadcast traffic and contain potential attacks.
- Educate users on accessing network resources using correct, fully qualified domain names (FQDN).

---


---
**🔍 Tags:** `#CyberDefenders` `#NetworkForensics` `#LLMNR_Poisoning` `#NBTNS` `#Wireshark` `#BlueTeam` `#DFIR`
