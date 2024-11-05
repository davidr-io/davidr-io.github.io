---
layout: post
title: Footprinting Lab - Easy
categories:
- hackthebox
tags:
- CTF
- Easy
pin: true
image: "/assets/CTF/htb/binary.jpg"
date: 2024-11-04 23:34 -0500
---
#### This is the first of 3 boxes required to complete the Footprinting module of the CPTS Penetration Tester pathway on HackTheBox

## **Scenario**
 We were commissioned by the company Inlanefreight Ltd to test three different servers in their internal network. The company uses many different services, and the IT security department felt that a penetration test was necessary to gain insight into their overall security posture.

The first server is an internal DNS server that needs to be investigated. In particular, our client wants to know what information we can get out of these services and how this information could be used against its infrastructure. Our goal is to gather as much information as possible about the server and find ways to use that information against the company. However, our client has made it clear that it is forbidden to attack the services aggressively using exploits, as these services are in production.

Additionally, our teammates have found the following credentials "ceil:qwer1234", and they pointed out that some of the company's employees were talking about SSH keys on a forum.

The administrators have stored a flag.txt file on this server to track our progress and measure success. Fully enumerate the target and submit the contents of this file as proof.

### Find flag.txt

We will start out with a basic nmap scan to get an idea of what services we have running on this machine

![Initial scan](/assets/CTF/htb/footprinting/easy/first_scan.png)

We have ssh creds, but were missing our key. Let's see if we can connect to ftp and use our login 'ceil:qwer1234' to get more info

![Initial ftp](/assets/CTF/htb/footprinting/easy/initial_ftp.png)

We're connected!

But there's nothing of interest here. Let us run a deeper nmap scan against our ftp servers and get some more information on them.

Our command will look like this
```bash
sudo nmap -sC -sV 10.129.192.198 -p 21,2121
```

2121 is of particular interest since that is not a standard ftp port. We will scan both and see what we find. 

![Second ftp scan](/assets/CTF/htb/footprinting/easy/second_ftp_scan.png)

Along with the versions of our FTP servers, we see something intersting on the second - Ceil's FTP 

Let's connect

![ftp connected](/assets/CTF/htb/footprinting/easy/ftp_connect.png)

Just what we were looking for - ssh keys. Let's take them and go get what we came for.

![flag](/assets/CTF/htb/footprinting/easy/flag.png)

On to Footprinting - Medium!




