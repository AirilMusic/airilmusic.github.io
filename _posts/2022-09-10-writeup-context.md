---
layout: single
title: Context - Hack The Box
excerpt: "Context es una máquina de la sección Fortress de Hack The Box."
date: 2022-09-10
classes: wide
header:
  teaser: /assets/images/writeup-context/context.PNG
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
tags:  
  - HTB
  - TCP
  - 
---

![](/assets/images/writeup-context/context2.jpg)

Context es una máquina de la sección Fortress de Hack The Box en la que se pueden hackear un montón de cosas, así podemos practicar un montón de técnicas distintas en la misma máquina.

## PORT SCAN

TCP:

```
 > nmap -sV -sC -Pn -T5 -n --open 10.13.37.12

Starting Nmap 7.92 ( https://nmap.org ) at 2022-09-10 19:29 CEST
Nmap scan report for 10.13.37.12
Host is up (0.11s latency).
Not shown: 997 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE       VERSION
443/tcp  open  ssl/https
|_http-title: Home page - Home
| ssl-cert: Subject: commonName=WMSvc-SHA2-WEB
| Not valid before: 2020-10-12T18:31:49
|_Not valid after:  2030-10-10T18:31:49
|_http-server-header: Microsoft-IIS/10.0
1433/tcp open  ms-sql-s      Microsoft SQL Server 2019 15.00.2070.00; GDR1
| ms-sql-ntlm-info: 
|   Target_Name: TEIGNTON
|   NetBIOS_Domain_Name: TEIGNTON
|   NetBIOS_Computer_Name: WEB
|   DNS_Domain_Name: TEIGNTON.HTB
|   DNS_Computer_Name: WEB.TEIGNTON.HTB
|   DNS_Tree_Name: TEIGNTON.HTB
|_  Product_Version: 10.0.17763
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: TEIGNTON
|   NetBIOS_Domain_Name: TEIGNTON
|   NetBIOS_Computer_Name: WEB
|   DNS_Domain_Name: TEIGNTON.HTB
|   DNS_Computer_Name: WEB.TEIGNTON.HTB
|   DNS_Tree_Name: TEIGNTON.HTB
|   Product_Version: 10.0.17763
|_  System_Time: 2022-09-10T17:30:25+00:00
| ssl-cert: Subject: commonName=WEB.TEIGNTON.HTB
| Not valid before: 2022-06-01T08:57:37
|_Not valid after:  2022-12-01T08:57:37
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| ms-sql-info: 
|   10.13.37.12:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 GDR1
|       number: 15.00.2070.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: GDR1
|       Post-SP patches applied: false
|_    TCP port: 1433
|_clock-skew: mean: 9s, deviation: 0s, median: 8s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 86.48 seconds
```

El puerto `443/tcp` está abierto, y tiene corriendo el servicio `open ssl` es decir que es una `web https`.

También vemos que hay una `Base de datos` en el puerto `1433/tcp` y viendo que tanto en ese puerto como en los siguientes está corriendo un servicio de `Microsoft` da a entender que la máquina es `Windows`, pero eso lo comprobaremos más adelante.

Y también vemos que hay un servidor corriendo en el puerto `3389/tcp` por lo que probablemente es otra `web`.

OS:

Ya hemos visto que probablemente será una máquina `Windows`, pero vamos a comprobarlo. Para esto vamos a mandarle una `traza ICMP` y segun el `ttl` de la respuesta vamos a saber que `sistema operativo` tiene.

```

```
