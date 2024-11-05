---
layout: post
title: Footprinting Lab - Hard
categories:
- hackthebox
- hard
tags:
- CTF
pin: true
image: "/assets/CTF/htb/binary.jpg"
date: 2024-11-05 12:23 -0500
---
#### This is the last of 3 boxes required to complete the Footprinting module of the CPTS Penetration Tester pathway on HackTheBox

## **Scenario**
The third server is an MX and management server for the internal network. Subsequently, this server has the function of a backup server for the internal accounts in the domain. Accordingly, a user named HTB was also created here, whose credentials we need to access.

### Find HTB user credentials

Let us begin with a basic nmap scan to get an idea of what services are running here
![Initial scan](/assets/CTF/htb/footprinting/hard/first_scan.png)

Aside from ssh, this box is running IMAP/POP3.

Let's go ahead and run a more sophisticated scan against these 5 ports

```bash
sudo nmap -sV -sC -p22,110,143,993,995 10.129.23.248
```

We have three sets of creds found in the previous boxes. 
These are:
```
ceil:qwer1234
alex:lol123!mD
sa:87N1ns@slls83
```
Let's see if we can login to the mail server with any of these.

After trying all login creds against both services, we have no luck.

Let's try a UDP scan to see if any other ports may be open
```bash
sudo nmap -sV -sU 10.129.23.248
```
![UDP port found](/assets/CTF/htb/footprinting/hard/161_found.png)
![SNMP found](/assets/CTF/htb/footprinting/hard/snmp.png)

Interesting. SNMPv3 port open, let's investigate this further.

```bash
snmpwalk -v2c -c backup 10.129.23.248
```

Yields some interesting results. 
![snmpwalk1](/assets/CTF/htb/footprinting/hard/snmpwalk_1.png)
![snmpwalk2](/assets/CTF/htb/footprinting/hard/snmpwalk_2.png)

Looks like Tom's new password may have been logged and exposed here. Let's go back to our IMAP/POP3 and try to get access.

![Logged in](/assets/CTF/htb/footprinting/hard/imap_tom.png)

Success! 

After logging in to Tom's email and digging through the folders, we find an email containing his ssh private key! Big no-no.

![Keys found](/assets/CTF/htb/footprinting/hard/keys.png)

Using this key, we successfully ssh into the machine as Tom.

After looking around, we see that there is '.mysql_history', which indicates we should check for a sql db

After logging into mysql with Tom's credentials and a few queries, we are able to print the Users table and get our HTB Login!
![flag found](/assets/CTF/htb/footprinting/hard/flag.png)

This was a long but immensely helpful module, and certainly taught me more than any of the previous modules. On to the next!







