# Week 1 – Windows Logon Event Analysis (4624 & 4625)

## 📝 Overview
Authentication is one of the first places attackers test their luck.  
If they can guess, brute force, or steal credentials, they’re in.  
As a SOC analyst, one of your earliest alerts will often come from **Windows logon events**.  

In this lab, I explored **Event IDs 4624 (successful logon)** and **4625 (failed logon)**.  
The goal was simple: make Windows talk, capture the activity, and interpret what those events are telling us.

---

## 🔍 What I Did
- Enabled auditing so Windows records both successful and failed logons.  
- Created a test user account (`X-User`) to simulate activity.  
- Attempted both **failed logins** (wrong password) and **successful logins** (correct password).  
- Used Event Viewer to track what really happened under the hood.  

---

## 📸 Findings

### Failed Logon – Event ID 4625
![failed_logon](./failed_logon_4625.png)  
- Account targeted: `X-User`  
- Failure reason: *Unknown username or bad password*  
- Logon type: `2` (interactive – console login)  
- Source network address: `127.0.0.1`  

👉 This is exactly how brute-force attempts or password guessing would show up in logs.  

---

### Successful Logon – Event ID 4624
![successful_logon](./successful_logon_4624.png)  
- Account logged in: `X-User`  
- Authentication package: NTLM  
- Logon type: `2` (interactive login)  

👉 A normal logon looks harmless, but in a SOC, multiple successful logins after repeated failures could mean an attacker **finally got the password right**.  

---

### Filtered View
![filtered_view](./filtered_view.png)  
By filtering only for Event IDs `4624` and `4625`, the noise disappears.  
What’s left is a clear timeline of **who tried to log in, when, and whether they succeeded**.  

---

## 🎯 Why This Matters
In the real world:  
- **One or two 4625s** → probably a user mistyping their password.  
- **Dozens of 4625s** → brute force attack.  
- **4625 flood + sudden 4624** → attacker just broke in.  

SOC analysts use exactly this type of analysis to build correlation rules and trigger alerts in SIEMs.  

---

## 💡 Reflection
Before this, “logon events” were just numbers to me.  
Now, I understand how they tell a story: *a failed attempt, persistence, eventual success*.  
That story is what separates normal user behavior from a potential breach.  

