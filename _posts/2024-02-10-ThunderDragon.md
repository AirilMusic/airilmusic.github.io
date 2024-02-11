---
layout: single
title: Thunder Dragon
excerpt: "This is the user guide of my pentesting tool Thunder dragon."
date: 2024-02-10
classes: wide
header:
  teaser: /assets/images/ThunderDragon/ThunderDragon.webp
  teaser_home_page: true
  icon: 
categories:
  - Wifi-Hacking
  - Web-Hacking
  - Programacion
tags:  
  - Python
---

![](/assets/images/ThunderDragon/ThunderDragon.webp)

## INDEX

- [INTRODUCTION](#introduction)
- [HOW TO USE](#how)
- [ENUMERATION](#enumeration)
- [AUTOMATIC](#auto)
- [ATTACKS](#attacks)
- [WIFI HACKING](#wifi)
- [BLUETHOOD HACKING](#bluethood)
- [OTHER FUNCTIONS](#other)

<a id="introduction"></a>
## INTRODUCTION

My goal with this tool is to simplify and automate the processes of enumeration, attacks, and other cybersecurity tasks, making them accessible and user-friendly for both seasoned professionals and novices alike. Designed to evolve gradually, this project is a long-term endeavor, and I appreciate your patience and understanding for any initial shortcomings or errors.

For enhanced accessibility, the tool supports both Linux and Windows platforms, although certain features might be Linux-exclusive. This dual-platform compatibility ensures the tool's utility in a wide range of educational settings, from teaching basic pentesting and vulnerability assessment in computer science classes to demonstrating the critical importance of cybersecurity and risk management.

All designed with an intuitive user interface that streamlines complex tasks. 

**I do not take responsibility for the misuse that may be made of this tool.**

<a id="how"></a>
## HOW TO USE

Before starting to use this tool, we need to download it and install the requirements. To do this, we can download it manually and install the requirements found in requirements.txt using pip, or more quickly through the console in the following way:

Linux:

```bash
git clone https://github.com/AirilMusic/Pentesting-Hacking/tree/main/ThunderDragon
cd ThunderDragon
pip install -r requirements.txt
```

Windows:

```
git clone https://github.com/AirilMusic/Pentesting-Hacking/tree/main/ThunderDragon
cd ThunderDragon
pip install -r requirements.txt
pip3 install -r requirements.txt
```

And now we can run the tool:

```
python main.py
```

And now we can use the help command to see the options we can use (the image is just a reference, because over time I will be adding more options):

![](/assets/images/ThunderDragon/help.png)

And from here, it would just be a matter of following the instructions provided by the tool.

<a id="enumeration"></a>
## ENUMERATION

- [WHOIS](#e1)
- [PORT SCAN](#e2)
- [SCAN LOCALNET](#e3)
- [SUBDOMAINS](#e4)
- [FUZZING](#e5)
- [DIRECTORY MAP](#e6)
- [TEC ENUM](#e7)
- [WEBDAV ENUM](#e8)
- [SHOW METADATA](#e9)
- [IP LOOKUP](#e10)
- [CHECK CVE](#e11)

<a id="e1"></a>
### WHOIS

The `whois` function is used to obtain and display detailed information about a specific domain. This information includes the domain name, the owner, the creation, expiration, and last update dates, the name servers, the domain status (DNS), associated email addresses, the owning organization, physical address, city, state, country, postal code, and contact phone number.

![](/assets/images/ThunderDragon/whois.PNG)

<a id="e2"></a>
### POR SCAN

The `port_scan` option is used to perform a port scan on a specific IP address. By selecting it, you can specify a range of ports to scan, but you can also specify that all ports be scanned with "all" or, if you have not specified anything, the most common ports will be scanned. To make the scan a little less noisy, fragment the packets and, instead of completing the connection by returning an ACK, close the connection by sending an RST (SYN --> SYN ACK --> RST instead of ACK).

![](/assets/images/ThunderDragon/port_scan.PNG)

<a id="e3"></a>
### SCAN LOCALNET

The `scan_localnet` function performs a scan of the local network to identify active devices, using an IP address and its CIDR notation as input. It operates by sending pings to each possible IP address within the specified range, logging those addresses that respond successfully as active hosts. To know the CIDR and the IP of the network, it first displays all the networks we have access to, so we can see the IP, network mask, etc.

As a quick guide to what CIDR is, here's this table (although there are more possible CIDRs, this is quite general)

![](/assets/images/ThunderDragon/cidr.PNG)

![](/assets/images/ThunderDragon/scan_localnet.PNG)

<a id="e4"></a>
### SUBDOMAINS

The functionality described is a multifaceted approach for discovering subdomains of a specified domain, incorporating both passive and active scanning techniques. Initially, it employs passive methods by exploiting SSL certificate transparency logs to uncover subdomains without actively querying the target domain. This is complemented by attempts at DNS zone transfers, where the tool queries the domain's nameservers for a complete list of subdomains, a method that relies on potential misconfigurations in nameserver permissions.

For a more aggressive discovery, the tool performs brute-force attacks, generating subdomain names by combining a predefined list of potential names with the target domain and checking for their existence through HTTP(S) requests. This process is designed to identify active subdomains by evaluating the server's response to these requests.



