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
  - DNS
  - SQLi
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

Para ver que sistema operativo corre en la máquina víctima le mandaremos una `traza ICMP`, aquí podremos ver ttl=63, pero realmente es `ttl=64`, es decir, es una máquina `Linux`. Lo de que se le quita uno al ttl pasa porque la conexión con la máquina no es directa, sino que pasa por un nodo intermediario.


## PUERTO 53: DNS

Antes de empezar con la web, vamos a ver si encontramos algun dominio valido mediante los `DNS`, ya que el `puerto 53` esta abierto.

```
❯ dig @10.13.37.10 -x 10.13.37.10

; <<>> DiG 9.16.27-Debian <<>> @10.13.37.10 -x 10.13.37.10
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 32264
;; flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;10.37.13.10.in-addr.arpa.	IN	PTR

;; AUTHORITY SECTION:
37.13.10.in-addr.arpa.	604800	IN	SOA	www.securewebinc.jet. securewebinc.jet. 3 604800 86400 2419200 604800

;; Query time: 113 msec
;; SERVER: 10.13.37.10#53(10.13.37.10)
;; WHEN: Sat Jul 09 17:27:25 CEST 2022
;; MSG SIZE  rcvd: 109
```
Ahí encontramos el dominio `www.securewebinc.jet`, así que lo agregamos al `/etc/hosts` y lo pondremos en el navegador.

![](/assets/images/htb-writeup-jet/web-53.PNG)

Nos encontramos con esta web, y si bajamos nos encontramos con la `Flag`:

![](/assets/images/htb-writeup-jet/web-53-2.PNG)

Pues ya que estamos aquí por si acaso, vamos a mirar el código, pero no sé...

Vale, pues si, sí que había algo interesante, si vamos al final nos encontramos con estos dos enlaces, el primero no es demasiado interesante, pero el segundo sí.

![](/assets/images/htb-writeup-jet/web-53-3.PNG)

Aquí nos encontramos esta cadena que desencriptaremos con la web de `jsnice`:

```
eval(String.fromCharCode(102,117,110,99,116,105,111,110,32,103,101,116,83,116,97,116,115,40,41,10,123,10,32,32,32,32,36,46,97,106,97,120,40,123,117,114,108,58,32,34,47,100,105,114,98,95,115,97,102,101,95,100,105,114,95,114,102,57,69,109,99,69,73,120,47,97,100,109,105,110,47,115,116,97,116,115,46,112,104,112,34,44,10,10,32,32,32,32,32,32,32,32,115,117,99,99,101,115,115,58,32,102,117,110,99,116,105,111,110,40,114,101,115,117,108,116,41,123,10,32,32,32,32,32,32,32,32,36,40,39,35,97,116,116,97,99,107,115,39,41,46,104,116,109,108,40,114,101,115,117,108,116,41,10,32,32,32,32,125,44,10,32,32,32,32,101,114,114,111,114,58,32,102,117,110,99,116,105,111,110,40,114,101,115,117,108,116,41,123,10,32,32,32,32,32,32,32,32,32,99,111,110,115,111,108,101,46,108,111,103,40,114,101,115,117,108,116,41,59,10,32,32,32,32,125,125,41,59,10,125,10,103,101,116,83,116,97,116,115,40,41,59,10,115,101,116,73,110,116,101,114,118,97,108,40,102,117,110,99,116,105,111,110,40,41,123,32,103,101,116,83,116,97,116,115,40,41,59,32,125,44,32,49,48,48,48,48,41,59));
```

![](/assets/images/htb-writeup-jet/jsnice.PNG)

Pues a primera vista ya vemos un directorio que parece muy interesante: `/dirb_safe_dir_rf9EmcEIx/admin/stats.php` al cual si le quitamos el stats.php nos redirige a una página de `login` el cual obviamente vamos a intentar bypassear.

Pero antes de intentar bypassear el login vamos a ver el código fuente del login y ahí nos encontramos con otra flag: `JET{s3cur3_js_w4s_not_s0_s3cur3_4ft3r4ll}`

Vale, una ves hemos mirado el código ya podemos intentar bypassear el login. Como con `sqlmap` no me ha dejado hacer una `sql incection` en el panel del login, lo que haré sera capturar la petición con `burpsuite`, exportarla a un archivo llamado "login" y luego meter el archivo en sqlmap para que pruebe con ella.

```
> cat login         #este es el archivo donde he copiado la petición

POST /dirb_safe_dir_rf9EmcEIx/admin/dologin.php HTTP/1.1
Host: www.securewebinc.jet
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:99.0) Gecko/20100101 Firefox/99.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 19
Origin: http://www.securewebinc.jet
Connection: close
Referer: http://www.securewebinc.jet/dirb_safe_dir_rf9EmcEIx/admin/login.php
Cookie: PHPSESSID=cff2hfr9uou4u70p2lqar4e8r6
Upgrade-Insecure-Requests: 1

username=uwu&password=uwu




```



## WEB: puerto 80 | http

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


## PUERTO 5555

Aquí también nos encontramos con otra web:

![](/assets/images/htb-writeup-jet/web-5555.PNG)

Y vemos que está cargando y no para, por lo que podemos intuir que hay algún proceso corriendo por detrás que quizás podamos explotar.


## PUERTO 7777

Aquí también nos encontramos con otra web:

![](/assets/images/htb-writeup-jet/web-7777.PNG)
