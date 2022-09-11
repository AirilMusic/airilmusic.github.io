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

(Por cierto, que en los writeups anteriores no lo puse y quizás es interesante. No tomo `medidas de seguridad` para hacer `menos ruido` y ser `menos detectable` porque es un CTF, pero sino, lo primero seria que el parámetro `-T 5` obviamente seria entre `-T 0` a `-T 3` y `fragmentaria` los paquetes en `paquetes de 8 bites` que se irían mandando poco a poco con el parámetro `-ff` (a la vez que haria uso de una `vpn` o `vps` para cambiarme la ip) `ESTO SE APLICA A TODOS LOS ESCANEOS PERO CON PARÁMETROS DISTINTOS`)

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

Ya hemos visto que probablemente será una máquina `Windows`, pero vamos a comprobarlo. Para esto vamos a mandarle una `traza ICMP` y según el `ttl` de la respuesta vamos a saber que `sistema operativo` tiene.

```
❯ ping -c 1 10.13.37.12

PING 10.13.37.12 (10.13.37.12) 56(84) bytes of data.
64 bytes from 10.13.37.12: icmp_seq=1 ttl=127 time=105 ms

--- 10.13.37.12 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 105.403/105.403/105.403/0.000 ms
```

Vemos que el ttl = 127 aunque en la realidad es `ttl = 128` porque la conexión no es directa, sino que pasa por un host intermediario, lo cual le quita uno de ttl. Por lo que podemos saber que estamos ante una máquina `Windows`.

(También he hecho un escaneo de los puertos que hay por el protocolo UDP, pero como no hay ningún puerto abierto he decidido no ponerlo aquí ya que no es interesante)

(Y no voy a probar el eternalblue, ya que nmap no lo detecta y al ser un fortress dudo que sea un windows antiguo)

## PUERTO 443

Si vamos a la web encontramos esto:

![](/assets/images/writeup-context/web-1.PNG)

Si clicamos donde pone lo de contacto, cuando nos redirija encontraremos que hace mención a un login de admin, y así podemos encontrar ese login que veremos más tarde.

Volviendo a la página principal no encontramos gran cosa, pero primero vamos a ver el `código` de la página, a ver si encontramos algo interesante o `virtual host routing` es decir, que haya varios servidores en el mismo host... Pero no encontramos gran cosa. Lo que si, si nos fijamos, la mayoría de cosas están en un directorio `/Home` por lo que vamos a hacer `wfuzzing` a ese directorio para ver que encontramos.

```
❯ wfuzz -c --hc=404 -t 200 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt https://10.13.37.12/Home/FUZZ

********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: https://10.13.37.12/Home/FUZZ
Total requests: 220560

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                     
=====================================================================

000000042:   200        99 L     209 W      2883 Ch     "products"
000000029:   200        59 L     245 W      2455 Ch     "privacy"
000000025:   200        63 L     145 W      2070 Ch     "contact"
000000245:   200        74 L     177 W      2137 Ch     "staff"
000000592:   200        63 L     145 W      2070 Ch     "Contact"
000000496:   200        99 L     209 W      2883 Ch     "Products"
000000616:   200        59 L     245 W      2455 Ch     "Privacy"
000000659:   200        78 L     232 W      2548 Ch     "Index"
000002614:   200        74 L     177 W      2137 Ch     "Staff"
000005085:   200        78 L     232 W      2548 Ch     "INDEX"
000023314:   200        63 L     145 W      2070 Ch     "CONTACT"
000043401:   200        59 L     245 W      2455 Ch     "PRIVACY"                                                                 
000071101:   200        99 L     209 W      2883 Ch     "PRODUCTS"  
```

Principalmente, me llama la atención el directorio `/Home/staff` vamos y vemos unos cuantos nombres que probablemente nos sirvan para `usernames` en algún lado.

![](/assets/images/writeup-context/web-staff.PNG)

También si vamos al `código` no encontramos gran cosa. Pero lo que sí que encontramos es la primera FLAG: `CONTEXT{s3cur1ty_thr0ugh_0bscur1ty}`

![](/assets/images/writeup-context/web-code.PNG)

A demás, vemos que un poco más abajo hace referencia al directorio `/Admin` que para entrar nos tenemos que loguear (no nos hace un redirect, por lo que `no parece vulnerable a Idor`) pero antes de probar algunos ataques como `Idor` o `SQLi` nos podemos fijar en otra cosa. En el mismo mensaje donde está la flag también encontramos unas credenciales `jay.teignton:admin` así que vamos a probarlas.
