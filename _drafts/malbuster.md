---
layout: post
title: MalBuster - Introductory Static Analysis
categories:
- CTF
- TryHackMe
tags:
- CTF
date: 2024-08-05 11:07 -0400
image: /assets/CTF/thm/malbuster/malware.jpg
---
This is my first TryHackMe room writeup published on this blog. While I am used to keeping detailed notes for my own refrence, I am still learning how to convey them in a way that is engaging and informative for the reader. If you have any tips or reccomendations, please don't hesitate to reach out to me on LinkedIn!

## Introduction
This is the first CTF-style challenge of the Malware Analysis learning module from TryHackMe. It is designed to be a practical application of the skills learned in the earlier rooms from this learning module. The recommended prerequisites for this room are:
- Intro to Malware Analysis
- Dissecting PE Headers
- Basic Static Analysis

Those three rooms alone provide a great foundation for PE file analysis and the basics of static analysis. *I highly recommend taking a look at those if you haven't already.*

### Scenario:
'You are currently working as a Malware Reverse Engineer for your organisation. Your team acts as a support for the SOC team when detections of unknown binaries occur. One of the SOC analysts triaged an alert triggered by binaries with unusual behaviour. Your task is to analyse the binaries detected by your SOC team and provide enough information to assist them in remediating the threat.'

### VMs 
This room provides us with the choice of using one of two Malware Analysis-focused VMs, FLARE VM and REMnux. These are both Malware Reverse Engineering focused distributions that come preinstalled with a suite of tools for Malware Analysis. If you would like more information on these VMs --> [FLARE VM](https://github.com/mandiant/flare-vm) [REMnux](https://remnux.org/?ltclid=)

I personally used FLARE VM for this since I am most comfortable with some of the PE analysis tools on Windows, but REMnux will get the job done if you prefer the Linux box. 

>You probably know this, but do not download the malware samples to your host.

{: .prompt-warning}

### Room Walkthrough

**Question 1**: Based on the arch of the binary, is malbuster_1 a 32-bit or 64-bit application?
<br>
**Answer 1**: 32-bit. We can quickly identify this using PEstudio and checking the 'cpu' information row.

**Question 2**: What is the md5hash of malbuster_1?
<br>
**Answer 2**: PEstudio provides us with the 3 major hashes (md5sum, sha1sum, sha256sum). md5sum:  4348DA65E4AEAE6472C7F97D6DD8AD8F

**Question 3**: Using the hash, what is the number of detections of malbuster_1 in VirusTotal?
*note*: If you have not checked out [VirusTotal](https://www.virustotal.com/gui/home/upload), I highly suggest giving it a look. Not only can you upload files (careful with this one, as you don't want to leak sensitive information), but you can provide URLs and even file hashes to get a quick, comprehensive report on the suspected malware. 
<br>
**Answer 3**: 62 (at the time of making this blog. These numbers change regularly)

**Question 4**: malbuster_2 imports the function _CorExeMain_. From which DLL file does it import this function?
<br>
**Answer 4**: mscoree.dll. For this, I just used the 'functions' tab in PEstudio to locate CoreExeMain. It is conveniently 1/849 functions listed for us. 

**Question 5**: Based on the VS_VERSION_INFO header, what is the original name of malbuster_2?
<br>
**Answer 5**: 7JYpE.exe. We could probably find this in PEstudion or PE-bear, but our report from VirusTotal gives us this information under 'Signature Info'. 

**Question 6**: Using the hash of malbuster_3, what is the malware signature based on abuse.ch?
<br>
**Answer 6**: TrickBot. 

**Question 7**: Using the hash of malbuster_4, what is its malware signature based on abuse.ch?
<br>
**Answer 7**: Zloader

**Question 8**: What is the message found in the DOS_STUB of malbuster_4?

![Desktop View](/assets/CTF/thm/malbuster/pe-bear.jpg){: .right }
_DOS_STUB dump in pe-bear_

<br>

**Answer 8**: Using PEstudio, the DOS stub message field yields 'N/A'. We can use PE-bear to take a closer look at it using the raw hex from the header. The message is '!This Salfram cannot be run in DOS mode.'

**Question 9**: malbuster_4 imports the function ShellExecuteA. From which DLL file does it import this function?
<br>

**Answer 9**: Looking at the 'imports' tab in PEstudio, it seems that the function names are displayed as ordinal values. This makes it a bit more challenging for us to find this function by name. We can throw this file into Ghidra to get a better view of the imports/exports. Looking at the Symbol Tree > Imports, we see a DLL import named 'Shell32.DLL'. This is our library in question. 


**Question 10**: Using capa, how many anti-VM instructions were identified in malbuster_1?
*note*: if you haven't used capa, it's a quick way to analyze and identify capabilities an executable has. 
![Desktop View](/assets/CTF/thm/malbuster/capa.jpg){: .right }
_capa report for malbuster_1_

<br>

**Answer 10**: 3

**Question 11**: Using capa, what is the MITRE ID of the DISCOVERY technique used by malbuster_4?
<br>

**Answer 11**: Capa provides this under the DISCOVERY section. T1083

**Question 12**: Which binary contains the string GodMode?
<br>

**Answer 12**: On Linux we can just use strings *file* and grep GodMode, but on Windows we'll have to use the *strings.exe* tool. We can redirect the output to str_x files, open them in Vim, and perform a quick search to find our answer. Malbuster_2

**Question 13**: Which binary contains the string **Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1)**?
<br>

**Answer 13**: Same technique as question 12. Malbuster_1

### Summary
Overall, this room, while slow paced, is great to get hands on with doing initial fingerprinting of suspected malware. Bookmark and download the tools used in this room, as they will reappear as we continue diving deeper into the realm of Malware Analysis. 
