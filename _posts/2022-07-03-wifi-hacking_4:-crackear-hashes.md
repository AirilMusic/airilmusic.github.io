---
layout: single
title: Wifi Hacking 4, Crackeo de hashes
excerpt: "En este artículo veremos como crackear los hashes que hemos obtenido para así lograr saber la contraseña."
date: 2022-07-08
classes: wide
header:
  teaser: 
  teaser_home_page: true
  icon: /assets/images/Wifi-Hacking/wifi.webp
categories:
  - Wifi-Hacking
tags:  
  - Wifi-Hacking
---

![](/assets/images/Wifi-Hacking/cracking.jpg)

Este es el último paso que tenemos que realizar si `lo que queremos lograr es la contraseña` de la red. Para esto hay varias técnicas y dentro de las mismas hay otras sub técnicas con las que podremos acelerar el proceso. Por eso aquí veremos todas, aunque `con cualquiera se puede lograr el mismo resultado`, vosotros elegid la que prefiráis.

Todas las técnicas que veremos funcionan a base de `fuerza bruta`, es decir que utilizando un `diccionario` que viene a ser una lista de palabras/contraseñas filtradas/... va probando una a una hasta que encuentre la correcta.

`IMPORTANTE: no es raro que la contraseña sea un número de teléfono, por lo que no está mal probar con eso si no conseguimos resultado con otros diccionarios, si necesitas un diccionario con números de teléfono o alguna otra cosa puedes mirar este simple script que programé hace tiempo: https://github.com/AirilMusic/Pentesting-Hacking/blob/main/Passwords-and-Brute-Forcing/DictionaryCreator.py`

##John the Reaper

Vamos a empezar explicando como hacerlo con esta conocida herramienta. Pero como John no está especializado en wifi hacking primero tenemos que preparar el hash, es decir, extraerlo del archivo .cap:

```
# generar miCaptura.hccap, parte del paquete con las creds
> aircrack-ng -J miCaptura Captura-01.cap (esto hace el hccap pero en binario)

# genera hash de las creds
> hccap2john miCaptura.hccap > miHash (esto lo pasa a json, solo falte desencriptarlo)
```

Una vez hecho esto ya podremos crackear la contraseña:

```
> john --wordlist={diccionario.txt} miHash
```
