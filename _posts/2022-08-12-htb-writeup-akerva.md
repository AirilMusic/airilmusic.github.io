---
layout: single
title: Akerva - Hack The Box
excerpt: "Akerva es una máquina de la sección Fortress de Hack The Box."
date: 2022-08-12
classes: wide
header:
  teaser: /assets/images/htb_writeup_akerva/Akerva.PNG
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - infosec
tags:  
  - HTB
  - 
---

![](/assets/images/htb_writeup_akerva/Akerva.PNG)
Akerva es una máquina de la sección Fortress de Hack The Box en la que se pueden hackear un montón de cosas, así podemos practicar un montón de técnicas distintas en la misma máquina.

## PORT SCAN

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


## PUERTO 80

Si vamos a su sitio web veremos esto:

![](/assets/images/htb_writeup_akerva/web-80.PNG)

En un principio puede parecer que no hay gran cosa, pero si nos fijamos en la parte de abajo de la web tenemos una información muy útil. El gestor de contenido de la web es `WordPress` y si miramos con `Wappalizer` veremos lo mismo, es decir que ya sabemos por donde tirar.

