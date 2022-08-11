---
layout: single
title: Akerva - Hack The Box
excerpt: "Akerva es una máquina de la sección Fortress de Hack The Box."
date: 2022-08-12
classes: wide
header:
  teaser: /assets/images/htb_writeup_akerva/akerva.PNG
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - infosec
tags:  
  - HTB
  - WordPress
---

![](/assets/images/htb_writeup_akerva/akerva2.png)
Akerva es una máquina de la sección Fortress de Hack The Box en la que se pueden hackear un montón de cosas, así podemos practicar un montón de técnicas distintas en la misma máquina.

## PORT SCAN

TCP:

```
> nmap -sV -sC -Pn -n -T5 10.13.37.11 --open

Starting Nmap 7.92 ( https://nmap.org ) at 2022-08-11 21:09 CEST
Nmap scan report for 10.13.37.11
Host is up (0.11s latency).
Not shown: 925 closed tcp ports (conn-refused), 72 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 0d:e4:41:fd:9f:a9:07:4d:25:b4:bd:5d:26:cc:4f:da (RSA)
|   256 f7:65:51:e0:39:37:2c:81:7f:b5:55:bd:63:9c:82:b5 (ECDSA)
|_  256 28:61:d3:5a:b9:39:f2:5b:d7:10:5a:67:ee:81:a8:5e (ED25519)
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-generator: WordPress 5.4-alpha-47225
|_http-title: Root of the Universe &#8211; by @lydericlefebvre &amp; @akerva_fr
|_http-server-header: Apache/2.4.29 (Ubuntu)
5000/tcp open  http    Werkzeug httpd 0.16.0 (Python 2.7.15+)
| http-auth: 
| HTTP/1.0 401 UNAUTHORIZED\x0D
|_  Basic realm=Authentication Required
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
|_http-server-header: Werkzeug/0.16.0 Python/2.7.15+
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 32.05 seconds
```

Podemos ver que el `puerto 22` está abierto, es decir `ssh`, aunque eso es normal en estas máquinas, ya que en muchas lo podemos utilizar si tenemos alguna `credencial` o `ssh key`, para establecer conexión con esa máquina desde ese puerto.

Por otra parte, también vemos el `puerto 80` por lo que podemos suponer que tiene una página `web`.

Y también el `5000 http`, es decir, otro servicio `web`, pero en este vemos un que tiene una cosita bastante interesante que es `Werkzeug`, así que es algo que tenemos que tener en cuenta luego al ver a fondo ese puerto, ya que esa versión puede tener alguna vulnerabilidad.

También `nmap` ya nos dice que es una máquina Linux, pero por si acaso vamos a mandarle una `traza ICMP` para mediante el `ttl: time to live` poder saber cuál es el sistema operativo `OS` con más exactitud.

```
> ping -c 1 10.13.37.11

PING 10.13.37.11 (10.13.37.11) 56(84) bytes of data.
64 bytes from 10.13.37.11: icmp_seq=1 ttl=63 time=106 ms

--- 10.13.37.11 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 105.970/105.970/105.970/0.000 ms
```

Vemos que el ttl = 63 aunque en la realidad es `ttl = 64` porque la conexión no es directa, sino que pasa por un host intermediario, lo cual le quita uno de ttl. Por lo que podemos saber que estamos ante una máquina Linux.

UDP:

```
❯ sudo nmap -sU --open -T5 --top-ports 200 10.13.37.11
Nmap scan report for 10.13.37.11
PORT      STATE         SERVICE
161/udp   open          snmp
```

Con `snmpbulkwalk` podemos ver la informacion que haya en ese puerto, y en este caso nos da una FLAG: `AKERVA{IkN0w_SnMP@@@MIsconfigur@T!onS}`

```
❯ snmpbulkwalk -v2c -c public 10.13.37.11 | grep AKERVA

iso.3.6.1.2.1.25.4.2.1.5.1243 = STRING: "/var/www/html/scripts/backup_every_17minutes.sh AKERVA{IkN0w_SnMP@@@MIsconfigur@T!onS}
```

## PUERTO 80

Si vamos a su sitio web veremos esto:

![](/assets/images/htb_writeup_akerva/web-80.PNG)

En un principio puede parecer que no hay gran cosa, pero si nos fijamos en la parte de abajo de la web tenemos una información muy útil. El gestor de contenido de la web es `WordPress` y si miramos con `Wappalizer` veremos lo mismo, es decir que ya sabemos por donde tirar.

También es verdad que en la izquierda podemos ver unos nombres que quizás más adelante pudrían ser usuarios válidos, pero de momento no nos son de utilidad.

Antes de empezar a hacer pruebas voy a mirar el código, aver si hay algún subdominio/directorio/... interesante y ya de paso a ver si hay alguna flag. Y resulta que no hay nada interesante, pero nos encontramos otra FLAG: `AKERVA{Ikn0w_F0rgoTTEN#CoMmeNts}`

![](/assets/images/htb_writeup_akerva/flag-1.PNG)

Ahora ya podemos empezar a hacer `reconocimiento` de la web a ver si encontramos algo interesante.

Para empezar podemos mirar con `Wappalizer` y ahí encontramos un par de cosas interesantes:

![](/assets/images/htb_writeup_akerva/wappalizer.PNG)

Podemos ver que hay una base de datos, `MySQL` y sabiendo que el gestor es un `Wordpress`, pues huele mucho a que en algún punto es vulnerable a `SQL injection`. Ya que los Worpress son muy propensos a tener esa clase de vulnerabilidades.

También podemos ver que tiene `JQuery`, pero no es de una versión vulnerable a `Prototype Pollution`, ya que el último artículo que hice fue sobre eso y me hacía ilusión XD, pero por si acaso voy a mirar a ver si es vulnerable a otras cosas. No, no parece tener ninguna vulnerabilidad asociada a esas versiones.

También podemos probar con `Whatweb` a ver si vemos algo interesante:

```
> whatweb http://10.13.37.11/

http://10.13.37.11/ [200 OK] Apache[2.4.29], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.29 (Ubuntu)], IP[10.13.37.11], JQuery, MetaGenerator[WordPress 5.4-alpha-47225], PoweredBy[@lydericlefebvre,WordPress], Script, Title[Root of the Universe &#8211; by @lydericlefebvre &amp; @akerva_fr], UncommonHeaders[link], WordPress, x-pingback[http://10.13.37.11/xmlrpc.php]
```

Pero no nos dice nada que no supiesemos :(

Pues vamos a intentar encontrar directorios con `Wfuzz`:

```
❯ wfuzz -c --hc=404 -t 200 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt http://10.13.37.11/FUZZ

********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://10.13.37.11/FUZZ
Total requests: 220560

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                     
=====================================================================

000000241:   301        9 L      28 W       315 Ch      "wp-content"                                                                                                                
000000274:   401        14 L     54 W       458 Ch      "scripts"                                                                                                                                                                                                                                    
000000786:   301        9 L      28 W       316 Ch      "wp-includes"                                                                                                               
000000834:   301        9 L      28 W       308 Ch      "dev"                                                                                                                       
000001073:   301        9 L      28 W       315 Ch      "javascript"                                                                                                                
000007180:   301        9 L      28 W       313 Ch      "wp-admin"                                                                                                                  
000011260:   301        9 L      28 W       312 Ch      "backups"                                                                                                                   
000095524:   403        9 L      28 W       276 Ch      "server-status"       
```

Archivos .php :

```
❯ wfuzz -c --hc=404 -t 200 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt http://10.13.37.11/FUZZ.php

********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://10.13.37.11/FUZZ.php
Total requests: 220560

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                     
=====================================================================
                                                                                     
000000475:   200        86 L     308 W      5036 Ch     "wp-login"                                                                                                                  
000000015:   301        0 L      0 W        0 Ch        "index"                                                                                                                                                  
000005002:   200        4 L      15 W       135 Ch      "wp-trackback"                                                                                                              
000017049:   405        0 L      6 W        42 Ch       "xmlrpc"                                                                                                               
000046026:   302        0 L      0 W        0 Ch        "wp-signup"                                                                                       
```

He probado con un `Idor` en el directorio `/wp-admin`, ya que hacía un `redirect al login` y ha funcionado, he `bypasseado el login`, pero la página estaba en blanco, es muy raro, supongo que no irán por ahí los tiros. Así que no merece la pena explicar ahora esa vulnerabilidad, ya que me da un poco pereza y total, ya la explicaré en otro momento.
