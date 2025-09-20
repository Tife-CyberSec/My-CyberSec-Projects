# Week 1 – Windows Logon Event Analysis (4624 & 4625)

## 📝 Overview
Authentication is one of the first doors attackers test.  
If they can guess, brute force, or steal credentials—they’re in.  

As a SOC analyst, some of the earliest alerts you’ll handle come from **Windows logon events**.  
In this lab, I focused on two key Event IDs:  
- **4624 – Successful Logon**  
- **4625 – Failed Logon**  

The objective was to simulate both normal and suspicious activity, capture the evidence, and interpret what the logs reveal.

---

## 🔍 What I Did
- Enabled auditing for successful and failed logons.  
  <img src="./Powershell Snip.png" alt="Audit Policy Enabled" width="500"/>  

- Created a test user account (`X-User`) for simulations.  
- Generated activity by attempting:  
  - **Failed logins** (wrong password).  
  - **Successful logins** (correct password).  
- Tracked the evidence in **Event Viewer**.  
  <img src="./Event Viewer Snip.PNGg" alt="Successful Login" width="500"/>  
  <img src="./Event Viewer Snip2.PNG" alt="Failed Login" width="500"/>  

---

## 📸 Findings

### 🔴 Failed Logon – Event ID 4625
<img src="./Powershell Snip3.png" alt="Failed logon PowerShell" width="500"/>  
- Account targeted: `X-User`  
- Failure reason: *Unknown username or bad password*  

<img src="./Powershell Snip4.png" alt="Failed logon details" width="500"/>  
- Logon type: `2` (interactive – console login)  
- Source network address: `127.0.0.1`  

👉 This is how brute-force attempts or password guessing appear in logs.  

---

### 🟢 Successful Logon – Event ID 4624
<img src="./Event Viewer Snip2.PNG " alt="Successful logon" width="500"/>  
- Account logged in: `X-User`  
- Authentication package: NTLM  
- Logon type: `2` (interactive)  

👉 By itself, this looks normal. But in a SOC scenario, multiple `4625` failures followed by a `4624` could mean an attacker finally cracked the password.  

---

### 🎛 Filtering Events
After filtering only for `4624` and `4625`, the noise dropped away.  
What remained was a clear timeline of:  
- **Who tried to log in**  
- **When it happened**  
- **Whether they succeeded**  

---

## Why This Matters
In real-world monitoring:  
- **1–2 x 4625s** → likely a user mistyping their password.  
- **Dozens of 4625s** → possible brute force attempt.  
- **4625 flood + sudden 4624** → attacker just broke in.  

SOC teams build rules and alerts around exactly this type of behavior.  

---

## Reflection
Before this, logon events felt like random numbers.  
Now I see how they tell a story: *failed attempts, persistence, and eventual success*.  

That story is what transforms raw logs into **actionable intelligence**.  
