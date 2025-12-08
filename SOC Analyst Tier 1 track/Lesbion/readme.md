## 🔍 Lespion Lab – Write-Up

Link to this lab: https://cyberdefenders.org/blueteam-ctf-challenges/lespion/


<img width="975" height="516" alt="image" src="https://github.com/user-attachments/assets/1c529d6e-d6bc-4efb-b719-27dc5e8dcf3a" />


## 📌 Scenario

You have been tasked by a client whose network was compromised and brought offline to investigate the incident and determine the attacker's identity. Preliminary findings indicate the attack originated from a single user account, likely an insider.


### **Methodology**
- GitHub repository analysis
- Social media correlation (Instagram, LinkedIn)
- Image metadata and reverse image search
- Geographic mapping using open-source tools

---

## 📊 Findings & Evidence

### **Q1: Exposed API Key**

**Finding:** `aJFRaLHjMXvYZgLPwiJkroYLGRkNBW`  


<img width="975" height="280" alt="image" src="https://github.com/user-attachments/assets/4cc12a3c-88be-4b28-8bf9-9ebdcde0b073" />


**Source:** GitHub repository `Project-Build---Custom-Login-Page` → `LoginPage.js`  

<img width="975" height="516" alt="image" src="https://github.com/user-attachments/assets/d3d37bb5-b7dd-444f-b792-6dea4a5032db" />

**Location:** Line containing `API Key = `  

**Risk:** Unauthorized access to integrated services

### **Q2: Exposed Plaintext Password**

**Finding:** `PicassoBaguette99`

<img width="975" height="205" alt="image" src="https://github.com/user-attachments/assets/b3d6bfcb-238a-4150-bbae-455b2ca0fb66" />

**Source:** Same GitHub file, Base64-encoded string `UGljYXNzb0JhZ3VldHRlOTk=`  

<img width="975" height="516" alt="image" src="https://github.com/user-attachments/assets/6f1dc724-307f-484a-9b53-c33ffe40c372" />

<img width="975" height="516" alt="image" src="https://github.com/user-attachments/assets/33b50c4d-2a78-4e2e-a281-d20f97c3dfa9" />

**Decoding Method:** Base64 decode via CyberChef or similar tool  

**Risk:** Password reuse across systems

### **Q3: Cryptocurrency Mining Tool**

**Finding:** `XMRig`  

<img width="975" height="199" alt="image" src="https://github.com/user-attachments/assets/6012cb41-a67c-4238-a005-1029830902fa" />

**Source:** GitHub repository overview  

<img width="975" height="486" alt="image" src="https://github.com/user-attachments/assets/c184149f-3b26-4ffe-bd05-59c134deb7b3" />

**Tool Purpose:** Monero (XMR) cryptocurrency mining  

**Implication:** Potential unauthorized resource utilization

### **Q4: Gaming Platform Account**

**Finding:** `Steam`  

<img width="975" height="217" alt="image" src="https://github.com/user-attachments/assets/8f061495-bc44-4649-98b5-7b8b10323e1d" />

**Source:** Instagram QR code scan  

<img width="975" height="474" alt="image" src="https://github.com/user-attachments/assets/d46d92d6-d703-4d1e-b162-7d84e9b5ba64" />

<img width="496" height="653" alt="image" src="https://github.com/user-attachments/assets/46d68e9a-a951-42ca-9127-c11c78dc32e2" />

<img width="608" height="1350" alt="image" src="https://github.com/user-attachments/assets/f3ad663d-f358-46e5-8c7f-0d068fb20c49" />


**Evidence:** QR code in Instagram profile resolves to Steam profile  

**Correlation:** Links personal hobby to professional identity

### **Q5: Instagram Profile**

**Finding:** `https://www.instagram.com/emarseille99/`  


<img width="975" height="214" alt="image" src="https://github.com/user-attachments/assets/8e1a067e-715c-4c51-abe9-c3cde0e629e2" />


**Source:** Google dork search for username "EMarseille99" 


<img width="975" height="494" alt="image" src="https://github.com/user-attachments/assets/9331d79d-3479-4957-8a93-430b3c164a3f" />


**Key Information:** Profile name "Émilie Marseille", location hints, personal photos

### **Q6: Holiday Destination**

**Finding:** `Singapore`  

<img width="975" height="226" alt="image" src="https://github.com/user-attachments/assets/3c8ab930-7b74-4a27-8b7d-48f57cba4b5e" />


**Source:** Instagram post reverse image search  

<img width="975" height="214" alt="image" src="https://github.com/user-attachments/assets/38999576-c1fb-4387-ad15-f20e775381cb" />

<img width="975" height="487" alt="image" src="https://github.com/user-attachments/assets/3f5df41f-6ed1-4161-8b1d-720b138fb1d3" />

<img width="975" height="480" alt="image" src="https://github.com/user-attachments/assets/d5628f65-fb00-4f1c-87eb-4d1705e49dd1" />

**Method:** Google Image Search of hotel photo → Marina Bay Sands identification  

**Timestamp:** Recent holiday activity

### **Q7: Family Residence**

**Finding:** `Dubai`  


<img width="975" height="196" alt="image" src="https://github.com/user-attachments/assets/eda1da3e-66ca-4463-80ea-618e237b6c64" />


**Source:** Instagram post with caption "Nice to meet friends & family"  

<img width="975" height="495" alt="image" src="https://github.com/user-attachments/assets/c2e2d754-e5c9-46dd-a45b-33f8bc4ecefb" />

<img width="975" height="336" alt="image" src="https://github.com/user-attachments/assets/c988b919-0f15-43ea-8b15-babfdff1d3c9" />

<img width="938" height="353" alt="image" src="https://github.com/user-attachments/assets/bfe4c73c-aff3-4f6e-aa2f-c6af3e51687b" />

<img width="975" height="559" alt="image" src="https://github.com/user-attachments/assets/2153b6ed-6b80-4e46-8e70-df3315831e03" />

<img width="975" height="465" alt="image" src="https://github.com/user-attachments/assets/364a1099-b0c7-488e-b3ae-0d4776489183" />

<img width="975" height="465" alt="image" src="https://github.com/user-attachments/assets/fdb9bc36-8cf9-40bb-8e60-3bf58a9ae55a" />

**Verification:** Reverse image search → YouTube video location tags  

**Confidence:** High (multiple correlating sources)

### **Q8: Company Office Location**

**Finding:** `Birmingham`  

**Source:** Provided `office.jpg` analysis  

<img width="975" height="498" alt="image" src="https://github.com/user-attachments/assets/e4c626ad-bd6d-466b-82b2-480db2c5c6db" />


<img width="975" height="363" alt="image" src="https://github.com/user-attachments/assets/3ec6faa0-3eeb-4a11-8de1-b5c59a248ada" />


<img width="975" height="483" alt="image" src="https://github.com/user-attachments/assets/1f9a41f9-7343-4ef0-aa8d-8dfd2bb41c62" />


**Method:** Landmark identification (Hippodrome Theatre, Alexandra Theatre) + "Chinese Quarter" reference  
**Verification:** Google Maps/Street View correlation

### **Q9: IP Camera Location**
**Finding:** `Indiana`  


<img width="975" height="300" alt="image" src="https://github.com/user-attachments/assets/730cc357-5510-4645-ab2a-d9e3594eed9c" />


**Source:** `Webcam.png` analysis  

<img width="975" height="463" alt="image" src="https://github.com/user-attachments/assets/ef80bc67-bbe7-4ddf-9313-95ca16fc7c08" />

**Method:** Earthcam "dome view" pattern recognition  

**Platform:** Earthcam network identification  

**Geographic Context:** Suspect traveled from UAE to US

---

## 🎯 Indicators of Compromise (IOCs)

### **Digital Identity**

- **GitHub:** `EMarseille99`
- **Instagram:** `emarseille99`
- **Real Name:** Émilie Marseille
- **LinkedIn:** Sorbonne University affiliation

### **Exposed Credentials**

- API Key: `aJFRaLHjMXvYZgLPwiJkroYLGRkNBW`
- Password: `PicassoBaguette99`
- Potential additional secrets in repositories

### **Tools & Artifacts**

- XMRig cryptocurrency miner
- Custom login page project
- Steam gaming account

### **Geographic Markers**

- Family: Dubai, UAE
- Holiday: Singapore
- Office: Birmingham, UK
- Current: Indiana, USA

---
<img width="975" height="471" alt="image" src="https://github.com/user-attachments/assets/9c147a15-4361-455a-b409-34aad96b274b" />


