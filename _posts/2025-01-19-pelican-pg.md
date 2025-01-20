---
layout: post
title: Pelican - Proving Grounds Practice
categories:
- offsec proving grounds
tags:
- oscp-prep
- linux
date: 2025-01-19 21:18 -0400
pin: true
image: "/assets/CTF/offsec_proving_grounds/pelican/hacker_blog_photo.png"
---
Pelican Writeup
---

#### Welcome to my walkthrough of the 'Pelican' Linux box from OffSec Proving Grounds Practice
- This is part of the LainKusanagi 'OSCP-Like' OSCP prep list, and is listed as 'intermediate' difficulty

### Enumeration
Run a basic nmap scan to see what services and ports are open
![basic nmap](/assets/CTF/offsec_proving_grounds/pelican/nmap_basic.png)

We can run a more sophisticated scan to get a better idea of what services and versions are running
![aggressive nmap](/assets/CTF/offsec_proving_grounds/pelican/nmap_aggressive.png)

### Jetty and Nginx
We have several interesting services, but after further enumeration of ssh, smb, and cups, we found nothing.

If we visit http://192.168.243.98:8080 we get an error 
![Jetty](/assets/CTF/offsec_proving_grounds/pelican/8080.png)

Visiting http://192.168.243.98:8081 redirects us to http://192.168.243.98:8080/exhibitor/v1/ui/index.html
![Exhibitor](/assets/CTF/offsec_proving_grounds/pelican/8081.png)

We quickly find a public exploit at https://www.exploit-db.com/exploits/48654, which is an arbitrary command execution vulnerability in the config control panel
![exploitdb](/assets/CTF/offsec_proving_grounds/pelican/exploitdb.png)

To execute system commands, we just need to replace what's in the java.env script with $(command to execute)
In this case, we will use a nc reverse shell to connect back to our machine and get our user flag
![config](/assets/CTF/offsec_proving_grounds/pelican/config.png)

I didn't receive a connection over 8081, but after switching to 80 (and updating the config on the web app) we receive the connection and get the user flag (or as OffSec calls it, local.txt)
![user flag](/assets/CTF/offsec_proving_grounds/pelican/user.png)

### Privilege Escalation

After doing some manual recon and checking our user's privileges, we find out we can use sudo with /usr/bin/gcore
![user privileges](/assets/CTF/offsec_proving_grounds/pelican/user_privs.png)

GTFOBins has a listing for this here:
https://gtfobins.github.io/gtfobins/gcore/#sudo

According to the Linux Manual Pages, gcore can "generate core dumps of one or more running programs with process IDs pid1, pid2, etc"

If we can find a process running with sensitive information in memory, we can generate a core dump and see if we can view anything in clear

Using 'ps aux' to list running processes, we see a process being ran by root, /usr/bin/password-store
![password-store](/assets/CTF/offsec_proving_grounds/pelican/password-store.png)

Password store is esentially a lightweight Unix/Linux password manager, also known as 'pass'

Let's generate a core dump of this process and see what we can find
![gcore](/assets/CTF/offsec_proving_grounds/pelican/gcore.png)

Password-store encrypts passwords using gpg and stores them away in disk. When the password is retreived, it is descrypted into plaintext using the user's private gpg key, and it is then stored in memory so it can be displayed, copied, etc by the user

Attempting to veiw the core. dump file, we are met with the encrypted data (and it is alot of it)
![encrypted dump](/assets/CTF/offsec_proving_grounds/pelican/encrypted_dump.png)

If we run 'strings core.494' against it, we can attempt to pull any cleartext out of this mess

Viola! Root password was hanging out in the memory of process 494, and we successfully found it using strings
![root pass](/assets/CTF/offsec_proving_grounds/pelican/root-pw.png)

su to root and get the flag!
![proof](/assets/CTF/offsec_proving_grounds/pelican/proof.png)

This was the 5th box on PG that I have done so far in OSCP prep. My goal is to fully complete all OSCP-Like from LainKusanagi List, which can be found here: https://docs.google.com/spreadsheets/d/18weuz_Eeynr6sXFQ87Cd5F0slOj9Z6rt/edit?gid=487240997#gid=487240997


