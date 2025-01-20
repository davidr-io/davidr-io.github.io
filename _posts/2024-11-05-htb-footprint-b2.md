---
layout: post
title: Footprinting Lab - Medium
categories:
- hackthebox
- medium
tags:
- CTF
pin: false
image: "/assets/CTF/htb/binary.jpg"
date: 2024-11-04 23:34 -0500
---
#### This is the second of 3 boxes required to complete the Footprinting module of the CPTS Penetration Tester pathway on HackTheBox

## **Introduction**
This second server is a server that everyone on the internal network has access to. In our discussion with our client, we pointed out that these servers are often one of the main targets for attackers and that this server should be added to the scope.

Our customer agreed to this and added this server to our scope. Here, too, the goal remains the same. We need to find out as much information as possible about this server and find ways to use it against the server itself. For the proof and protection of customer data, a user named HTB has been created. Accordingly, we need to obtain the credentials of this user as proof.

### Getting the flag

Upon doing a basic service scan, we see several open ports. Just going off of our hint above, I have a feeling we should begin this task by enumerating nfs
![Initial scan](/assets/CTF/htb/footprinting/medium/initial_scan.png)


Using showmount, we see there is a share 'TechSupport (everyone)' listed as available. Let's see if we're able to mount and confirm this does not contain sensitive data.
![nfs scan](/assets/CTF/htb/footprinting/medium/nfs_scan.png)

Let's go ahead and mount the share to our machine and see what we can find

Use this to mount the remote share 
```bash
sudo mount -t nfs -o resvport,rw,nolock 10.129.80.61:/TechSupport ./target-NFS/
```

![nfs mountd](/assets/CTF/htb/footprinting/medium/nfs_mounted.png)

As we see above, we have what appear to be tech support tickets. Most are empty, but one contains a clue

![Ticket](/assets/CTF/htb/footprinting/medium/ticket1.png)
![Ticket2](/assets/CTF/htb/footprinting/medium/ticket2.png)

Using these creds with enum4linux to enumerate SMB, we find 5 shares, all of which are interest to use
![enumSMB](/assets/CTF/htb/footprinting/medium/enum4linux.png)

If we revisit the description of this task, we are looking for HTB user login credentials. Querying server info tells us this is a SQL server. We're on the right track.

After trying each share, we get some info from the devshare
![SMB connected](/assets/CTF/htb/footprinting/medium/smb_connected.png)
![important.txt](/assets/CTF/htb/footprinting/medium/imporant.png)

We are able to successfully RDP into the machine using our orignal creds
![rdp](/assets/CTF/htb/footprinting/medium/rdp.png)

Here we find MSSQL Server Management Studio, which autofills username 'sa'.

This is where we can use our sysadmin creds and continue our search
![SQL Server Login](/assets/CTF/htb/footprinting/medium/SQL_server.png)

Once logged into the database management system, we need to query the accounts database for the HTB user creds

There we have it! HTB User credentials.
![flag](/assets/CTF/htb/footprinting/medium/flag.png)

On to the final box of this module!









