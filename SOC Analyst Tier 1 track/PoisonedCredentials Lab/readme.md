# 🛡️ PoisonedCredentials – LLMNR/NBT-NS Poisoning Analysis Write-up

## 📌 Lab Overview
**📝 Scenario:** Analyze network traffic to detect LLMNR/NBT-NS poisoning attacks, identify the rogue machine, compromised accounts, and affected systems.  
**🗂️ Category:** Network Forensics  
**🎯 Attack Techniques:** LLMNR/NBT-NS Poisoning, SMB Relay, Credential Theft  
**🛠️ Tools:** Wireshark  
**⚡ Difficulty:** Easy | ⏱ ~30 mins  
**🔗 Source:** [CyberDefenders – PoisonedCredentials](https://cyberdefenders.org/labs/75)

---

## 🧩 Analysis & Findings

### 🔹 Q1 – Identify Mistyped Query
**Objective:** Find the mistyped share name queried by `192.168.232.162`.  
**🔍 Analysis:** Applied Wireshark filter `llmnr and ip.src==192.168.232.162` to isolate the misspelled LLMNR query.  
**✅ Answer:** `fileshaare`

### 🔹 Q2 – Rogue Machine IP
**Objective:** Determine the IP address of the attacker's machine.  
**🔍 Analysis:** Located the LLMNR response packet sent immediately after the mistyped query, identifying the spoofing source.  
**✅ Answer:** `192.168.232.215`

### 🔹 Q3 – Second Affected Machine
**Objective:** Identify the second victim that received poisoned responses.  
**🔍 Analysis:** Filtered by attacker IP (`ip.addr==192.168.232.215`) and examined unique communication partners.  
**✅ Answer:** `192.168.232.176`

### 🔹 Q4 – Compromised Username
**Objective:** Find the username of the compromised account.  
**🔍 Analysis:** Filtered SMB2 traffic involving the attacker (`smb2 and ip.addr==192.168.232.215`) and extracted credentials from NTLMSSP_AUTH.  
**✅ Answer:** `janesmith`

### 🔹 Q5 – Accessed Hostname
**Objective:** Discover the hostname of the machine accessed via SMB.  
**🔍 Analysis:** Followed the TCP stream of the SMB session and inspected the `Session Setup` request for the target server name.  
**✅ Answer:** `AccountingPC`

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

## 📁 Files
- **PCAP File:** `PoisonedCredentials.pcap`
- **Write-up Author:** [Your Name/Alias]
- **Completion Date:** December 2025

---
**🔍 Tags:** `#CyberDefenders` `#NetworkForensics` `#LLMNR_Poisoning` `#NBTNS` `#Wireshark` `#BlueTeam` `#DFIR`
