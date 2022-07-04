---
layout: single
title: Wifi Hacking 2, preparación antes de atacar
excerpt: "Para efectuar un buen ataque y mantenernos en el anonimato primero debemos ocultar nuestro rastro. Y también tendremos que ver que redes hay e información de las mismas."
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

En este segundo artículo primero veremos como cambiar nuestra MAC para que no se nos pueda identificar al llevar a cabo los ataques. Y luego ya empezaremos a hacer cositas, primero vamos a ver las redes que están a nuestro alcance y diversa información útil de las mismas.


## FALSIFICAR NUESTRA MAC

Es importante hacer esto antes de efectuar cualquier ataque a una red, ya que si no lo hacemos la compañía o cualquiera que este snifanzo la red puede ver los paquetes que mandemos y ahí podría ver nuestra MAC, lo que nos delataría. Para que esto no suceda vamos a cambiar nuestra MAC:
```
> macchanger {tarjeta de red}               #(para ver la direccion mac)
(en una direccion mac, los tres primeros numeros identifican la tecnologia del dispositivo)

> macchanger -l                             #(para ver muchas mac)    ESTO SE PUEDE SALTAR

> macchanger -l | grep "NATIONAL SECURITY AGENCY"           #(para filtral las MAC por las de otra empresa/organicacion, en este caso la NSA)

> macchanger --mac={los primeros 3 pares de numeros de lo que hayas filtrado}{los siguientes 3 pares se pueden inventar} {el nombre de la tarjeta en modo monitor}

> ifconfig {tarjeta} down                   #(para darla de baja)

> macchanger -s {tarjeta}                   #(para ver la mac nueva)
```

