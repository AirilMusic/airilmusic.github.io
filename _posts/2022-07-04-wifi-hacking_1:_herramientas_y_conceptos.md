---
layout: single
title: Wifi Hacking 1, herramientas y conceptos
excerpt: "Este es el primer artículo sobre Wifi hacking y para introducirnos en el tema aquí expondré la herramienta principal que vamos a utilizar y los conceptos básicos que necesitaremos conocer."
date: 2022-07-04
classes: wide
header:
  teaser: 
  teaser_home_page: true
  icon:
categories:
  - Wifi-Hacking
tags:  
  - Wifi-Hacking
---

Para comenzar con este tema aquí tenéis el primer artículo en el cual vamos a ver la herramienta principal que vamos a utilizar y los conceptos básicos que necesitaremos conocer antes de empezar a tocar cosas.

## Herramienta: tarjeta de red

La herramienta principal que vamos a necesitar es una tarjeta de red, es importante que tenga la opción de modo monitor, ya que va a ser esencial para capturar y transmitir los paquetes de datos que viajan por el aire y que vamos a mandar, manipular y leer en los ataques que veremos más adelante. `IMPORTANTE: no voy a explicar como poner la tarjeta de red en modo monitor, ya que hay varios tipos de tarjetas y cada una tiene su método, por eso buscarlo vosotros en google o en youtube.` 
Es verdad que en algunos casos si estamos utilizando un ordenador de sobremesa quizás podemos usar la propia tarjeta de red que tenga el ordenador, pero por si acaso es recomendable comprar una externa de las que se conectan mediante USB.


Algunos modelos de tarjetas de red son:

-TP-LINK TL-WN722N (con estas es importante saber que hacen chanel hoppig por lo que a la hora de hacer ataques de tipo `Evil Twin Attack` puede complicarlos un poco)

-Alfa

![](/assets/images/Wifi-Hacking/TPL-TL-WN722N.jpg)

![](/assets/images/Wifi-Hacking/Alfa.jpg)


## Conceptos basicos

IP:

Una dirección IP es una dirección única que identifica a un dispositivo en Internet o en una red local. IP significa “protocolo de Internet”, que es el conjunto de reglas que rigen el formato de los datos enviados a través de Internet o la red local.

En esencia, las direcciones IP son el identificador que permite el envío de información entre dispositivos en una red. Contienen información de la ubicación y brindan a los dispositivos acceso de comunicación. Internet necesita una forma de diferenciar entre distintos dispositivos, enrutadores y sitios web / servidores. Forman una parte esencial de cómo funciona Internet.

A grandes rasgos podemos hacer una distinción:

´IP publica:´ es la ip que tenemos de cara a internet, es decir al exterior.

´-IP privada:´ es la ip que se nos asigna en una red, por ejemplo en una red wifi.

MAC: Es como el DNI de un dispositivo, osea, el identificador, pero se puede falsificar, al 	hacer ataques de red lo recomendable es cambiar la dirección MAC, por lo que en el 	"Current Mac" saldrá la dirección modificada pero en "Permanent Mac" saldrá la 	original del dispositivo.

AP: ´Acces Point´ es decir, el punto de acceso que tenemos a una red, por ejemplo un router o un móvil que esté compartiendo datos.

INTERFAZ DE RED: Una interfaz de red es una interfaz de software para hardware de red. El kernel de Linux distingue entre dos tipos de interfaces de red: físicas y virtuales. … En la práctica, a menudo encontrará la interfaz eth0, que representa la tarjeta de red Ethernet. O de otras palabras menos exactas y por las que cualquiera que sepa del tema me matara: ´el nombre de la tarjeta de red´.

![](/assets/images/Wifi-Hacking/interfaz_de_red.PNG)

HAND SHAKE:

En que consiste: tú estas como atacante desde fuera viendo el punto de acceso y los 	clientes que están conectados a él. Cuando un cliente se reconecta tú capturas los 	paquetes, y entre esos paquetes estará la contraseña de la red encriptada. Se puede 	hacer en modo de ataque pasivo, es decir, esperar a que alguien se reconecte, o de forma activa, desconectando y dejando que se vuelva a conectar un cliente mientras que tú estas a la escucha con airodump.

Para ver si has conseguido hand shakes se puede hacer con wireshark, de esta forma:
```
·wireshark captura-01.cap
y aplicar el filtro "eapol"
```


DICCIOINARIO:
Es un archivo que contiene un montón de palabras que se van a ir probando una tras otra cuando se está haciendo fuerza bruta mediante diccionario. Esto se puede utilizar para crackear contraseñas, encontrar rutas en la web, encontrar usuarios...
Algunos diccionarios se hacen con las filtraciones masivas de contraseñas, pero no todos se hacen así.
