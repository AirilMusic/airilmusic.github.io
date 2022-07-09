---
layout: single
title: Jet - Hack The Box
excerpt: "Delivery es una máquina de la sección Fortress de Hack The Box."
date: 2022-07-09
classes: wide
header:
  teaser: /assets/images/htb-writeup-jet/jet.PNG
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - infosec
tags:  
  - 
---

![](/assets/images/htb-writeup-jet/jet.PNG)

Delivery es una máquina de la sección Fortress de Hack The Box.

## PORT SCAN

```
> nmap -sV -sC -Pn -n -T5 -v 10.13.37.10 --open

22/tcp   open  ssh      OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 62:f6:49:80:81:cf:f0:07:0e:5a:ad:e9:8e:1f:2b:7c (RSA)
|   256 54:e2:7e:5a:1c:aa:9a:ab:65:ca:fa:39:28:bc:0a:43 (ECDSA)
|_  256 93:bc:37:b7:e0:08:ce:2d:03:99:01:0a:a9:df:da:cd (ED25519)
53/tcp   open  domain   ISC BIND 9.10.3-P4 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.10.3-P4-Ubuntu
80/tcp   open  http     nginx 1.10.3 (Ubuntu)
|_http-title: Welcome to nginx on Debian!
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-server-header: nginx/1.10.3 (Ubuntu)
5555/tcp open  freeciv?
| fingerprint-strings: 
|   DNSVersionBindReqTCP, GenericLines, GetRequest, adbConnect: 
|     enter your name:
|     [31mMember manager!
|     edit
|     change name
|     gift
|     exit
|   NULL: 
|     enter your name:
|   SMBProgNeg: 
|     enter your name:
|     [31mMember manager!
|     edit
|     change name
|     gift
|     exit
|     invalid option!
|     [31mMember manager!
|     edit
|     change name
|     gift
|     exit
|     invalid option!
|     [31mMember manager!
|     edit
|     change name
|     gift
|     exit
|     invalid option!
|     [31mMember manager!
|     edit
|     change name
|     gift
|     exit
|     invalid option!
|     [31mMember manager!
|     edit
|     change name
|     gift
|     exit
|     invalid option!
|     [31mMember manager!
|     edit
|     change name
|     gift
|     exit
|     invalid option!
|     [31mMember manager!
|     edit
|     change name
|     gift
|     exit
|     invalid option!
|     [31mMember manager!
|     edit
|     change name
|     gift
|     exit
|     invalid option!
|     [31mMember manager!
|     edit
|     change name
|     gift
|     exit
|_    invalid option!
7777/tcp open  cbt?
| fingerprint-strings: 
|   Arucer, DNSStatusRequestTCP, DNSVersionBindReqTCP, GenericLines, GetRequest, HTTPOptions, RPCCheck, RTSPRequest, Socks5, X11Probe: 
|     --==[[ Spiritual Memo ]]==--
|     Create a memo
|     Show memo
|     Delete memo
|     Can't you read mate?
|   NULL: 
|     --==[[ Spiritual Memo ]]==--
|     Create a memo
|     Show memo
|_    Delete memo
```
## OS
```
> ping -c 1 10.13.37.10

PING 10.13.37.10 (10.13.37.10) 56(84) bytes of data.
64 bytes from 10.13.37.10: icmp_seq=1 ttl=63 time=111 ms

--- 10.13.37.10 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 111.035/111.035/111.035/0.000 ms
```

Para ver que sistema operativo corre en la máquina víctima le mandaremos una `traza ICMP`, aquí podremos ver ttl=63, pero realmente es `ttl=64`, es decir, es uma máquina `Linux`. Lo de que se le quita uno al ttl pasa porque la conexión con la máquina no es directa, sino que pasa por un nodo intermediario.


## WEB

![](/assets/images/htb-writeup-jet/web.PNG)

Pues ahí tenemos la `primera flag` uwu

Si lo miramos poniendo la IP en el navegador, o añadiéndole el nombre jet.htb en el archivo `/etc/hosts` y luego poniendo `jet.htb` en el navegador nos va a salir lo mismo.

En `Wappalizer` es raro pero no sale nada, asi que probare con `whatweb`

```
> whatweb http://10.13.37.10

http://10.13.37.10/ [200 OK] Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][nginx/1.10.3 (Ubuntu)], IP[10.13.37.10], Title[Welcome to nginx on Debian!], nginx[1.10.3]
```

Pues empezamos bien, esa versión de nginx es vulnerable, he encontrado un exploit, pero en este caso no nos sirve, todo pinta a que esa página o va a aportarnos nada más, quizás hay alguna otra página o quizás hace `virtual host routing`, porque con `wfuzz` no vemos ningún directorio más (y eso que he probado con varias extensiones distintas: .php .html ...). Por si acaso miraré el código fuente de la página, pero sino pasaremos a otra cosa.

En el código fuente de la página no hay nada interesante por lo que dudo que se pueda hacer más en esa página.


Vale, siguiendo con la pagina web, por el puerto 5555 y por el pureto 7777 aparecen cosas distintas que es lo que nos estaba reportanto `nmap`.

Por el puerto 5555:

![](/assets/images/htb-writeup-jet/web-5555.PNG)

Y vemos que está cargando y no para, por lo que podemos intuir que hay algún proceso corriendo por detrás que quizás podamos explotar.

Por el puerto 7777:

![](/assets/images/htb-writeup-jet/web-7777.PNG)
