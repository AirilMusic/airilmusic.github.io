---
layout: single
title: WPS Kraken
excerpt: "En este artículo muestro la herramienta que he creado para pentesting de redes con wps y la explicación de como funciona."
date: 2023-02-28
classes: wide
header:
  teaser: /assets/images/wps_kraken/portada.png
  teaser_home_page: true
  icon: /assets/images/Wifi-Hacking/wifi.webp
categories:
  - Wifi-Hacking
  - Programacion
tags:  
  - Wifi-Hacking
  - WPS
  - Python
---

![](/assets/images/wps_kraken/portada.png)

## ÍNDICE

- [INTRODUCCION](#1)
- [EXPLICACIÓN TEÓRICA](#2)
- [PROTOCOLO WPS](#WPS)
- [PIXIE DUST](#3)
- [MEDIAS DE SEGURIDAD](#SEG)
- [EXPLICACIÓN DEL CÓDIGO Y DEL FUNCIONAMIENTO](#4)

```
                    ⠀⠀⠀⠀⠀⠀ ⠀ ⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣀⣀⣀⣀⣀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀                      ⠀⠀⣠⣶⣿⣿⣿⣿⣿⣿⣿⣿⣷⣦⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
                    ⠀⠀⠀⠀ ⠀⠀⠀⠀⠀⠀⠀⣀⣀⠀⠀⠀⢠⣾⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣦⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
                    ⠀⠀⠀⠀⠀⠀⠀⠀⢠⣾⡿⠿⢿⣿⣷⣠⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⡆⣷⣠⣴⣶⣶⣤⡀⠀⠀⠀⠀⠀⠀⠀⠀
                    ⠀⠀⠀⠀⠀⠀⠀⠀⠻⢿⡄⠀⠀⠹⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣧⣿⣿⣟⠉⢹⣿⣷⠀⠀⠀⠀⠀⠀⠀⠀
                    ⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢠⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠛⠿⠿⠿⠋⠀⠀⠀⠀⠀⠀⠀⠀
                    ⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣾⣿⡿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠉⣿⣿⣧⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
                    ⠀⠀⠀⢀⣀⣀⠀⠀⠀⠀⠀⣰⣿⣿⡟⠁⠘⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠃⠀⠈⢿⣿⣷⡄⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
                    ⠀⣴⣾⣿⣿⣿⣿⣶⡀⢀⣾⣿⣿⠋⠀⠀⠀⠈⠻⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠃⠀⠀⠀⠀⠹⣿⣿⣦⠀⠀⠀⠀⠀⠀⠀⠀⠀
                    ⢸⣿⡁⠀⠀⢀⣿⣿⢇⣾⣿⣿⠃⠀⠀⠀⠀⠀⠀⣿⡈⠙⢿⣿⣿⣿⠿⠋⢩⡇⠀⠀⠀⠀⠀⠀⠙⣿⣿⣇⠀⠀⠀⠀⠀⠀⠀⠀
                    ⠈⠛⠛⣠⣴⣿⡿⠋⢸⣿⣿⡇⠀⠀⠀⠀⠀⠀⠀⣿⣿⣶⣾⣿⣿⣿⣷⣶⣿⡇⠀⠀⠀⠀⠀⠀⠀⣻⣿⣿⠀⠀⠀⠀⠀⠀⠀⠀
                    ⠀⣠⣾⣿⡿⠋⠀⠀⢻⣿⣿⣷⡀⠀⠀⠀⠀⣠⣾⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣦⡀⠀⠀⠀⠀⢠⣿⣿⣏⣠⣤⣶⣤⠀⠀⠀⠀
                    ⢰⣿⣿⣟⠀⠀⠀⠀⠘⢿⣿⣿⣿⣷⣶⣶⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣶⣤⣤⣴⣿⣿⣿⣿⠋⠀⠀⠀⠀⠀⠀⠀
                    ⢸⣿⣿⣿⣦⣄⣀⠀⠀⠀⠉⠙⠛⠛⢿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⡿⠿⠛⠉⢻⣿⣄⠀⠀⠀⠀⠀⠀⠀
                    ⠀⠙⠿⣿⣿⣿⣿⣿⣿⣿⣶⣶⣶⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣦⡀⠀⠀⠀⠈⢿⣿⣶⣄⠀⠀⠀⠀⠀
                    ⠀⠀⠀⠀⠈⠉⠉⠙⠛⠛⠛⠛⠛⣿⣿⣿⣿⠟⢋⣿⣿⣿⡿⠋⠙⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣆⠀⠀⠀⠀⠙⢿⣿⣧⡀⠀⠀⠀
                    ⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀ ⢀⣾⣿⣿⠟⠁⠀⣿⣿⣿⠟⠀⠀⢀⣿⣿⣿⡿⢿⣿⣿⣿⣿⣿⣿⣆⠀⠀⠀⠀⠈⢿⣿⣷⠀⠀⠀
                    ⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣾⣿⣿⠏⠀ ⠀⢸⣿⣿⣿⠀⠀⠀⢸⣿⣿⣿⠀⠈⢻⣿⣿⣿⢿⣿⣿⣦⡀⠀⠀⠀⣸⣿⣿⠀⣀⡄
                    ⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣰⣿⣿⡟⠀⠀⠀⠸⣿⣿⣿⠀⠀⠀⢻⣿⣿⣿⠀⠀ ⠀⢻⣿⣿⡆⠹⢿⣿⣿⣶⣶⣾⣿⣿⣿⣿⠋⠀
                    ⠀⠀⠀⠀⠀⠀⠀⠀⢀⣴⣿⣿⡿⠁⠀⠀⠀⠀⢿⣿⣿⡆⠀⠀⠸⣿⣿⣿⡄⠀⠀⠀⢿⣿⣿⠀ ⠀⠙⠛⠿⠿⠿⠛⠋⢸⣿⠀⠀
                    ⠀⠀⠀⠀⠀⠀⣠⣴⣿⣿⡿⠛⠁⠀⠀⠀⠀⠀⠘⣿⣿⣿⠀⠀⠀⣿⣿⣿⡇⠀⠀⠀⢸⣿⣿⡇⠀⠀⠀⠀⠀⠀  ⠀⠀⢸⣿⠀⠀
                    ⠀⠀⠀⢠⣶⣿⣿⠿⠋⠁⠒⠛⢻⣷⠀⠀⠀⠀⠀⢹⣿⣿⡇⠀⣠⣿⣿⣿⢃⣴⣿⠟⠛⢿⣿⣿⡄⠀⠀⠀⠀⠀⠀⢠⣿⣿⠀⠀
                    ⠀⠀⢰⣿⣿⠟⠁⠀⠀⠀⠀⢀⣾⡟⠀⠀⠀⠀⠀⠘⣿⣿⣧⣾⣿⣿⠟⠁⣾⣿⡇⠀⠀⠘⢿⣿⣿⣦⡀⠀⠀⣀⣴⣿⣿⠃⠀⠀
                    ⠀⠀⣿⣿⡇⠀⠀⢀⡄⠀⢠⣿⣿⠀⠀⠀⠀⠀⠀⢰⣿⣿⣿⣿⠟⠁⠀⠀⢿⣿⣇⠀⠀⠀⠈⠻⣿⣿⣿⣿⣿⣿⡿⠟⠁⠀⠀⠀
                    ⠀⠀⠹⣿⣷⣄⣀⣼⡇⠀⢸⣿⣿⡀⠀⠀⠀⠀⣠⣿⣿⣿⡿⠋⠀⠀⠀⠀⢸⣿⣿⡀⠀⠀⠀⠀⠀⠉⠉⠉⠉⠁⠀⠀⠀⠀⠀⠀
                    ⠀⠀⠀⠈⠛⠛⠛⠋⠀⠀⠀⢻⣿⣿⣶⣶⣶⣿⣿⣿⣿⣿⠁⠀⠀⠀⠀⠀⠀⣿⣿⡇⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
                    ⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠛⠛⠛⠛⠛⠉⣿⣿⣿⡀⠀⠀⠀⠀⠀⠀⣿⣿⣿⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
                     ⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠸⣿⣿⣷⣄⣀⠀⢀⣀⣴⣿⣿⡇⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
                    ⠀ ⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠙⢿⣿⣿⣿⣿⣿⣿⣿⡿⠋⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀                     ⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠉⠉⠉⠉⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
```

<a id="1"></a>

Cuando estuve aprendiendo `wifi hacking` una de las ramas que no estudié en profundidad fué el protocolo `WPS`, por diversos motivos de complejidad... por lo que se me quedaron las ganas de aprender mas sobre el tema. Así que para aprender, un mes antes de navidad se me ocurrió hacer `una herramienta que automatizase diversos ataques` a las redes con este protocolo activo y así aprender como funciona todo a "bajo nivel". Aunque entre Bachiller y que me atasqué con la vulnerabilidad `Pixie Dust` no la he podido terminar hasta ayer a la tarde `(27/02/2023)`. 

También me parece recalcable (sobre todo si alguién quiere criticarme o yo que se), que esta herramienta la he hecho `con 17 años` con el único fin de `aprender`, por lo que no es una herramienta profesional y estoy segura de que se pueden mejorar y arreglar muchísimas cosas.  

Aquí dejo un video donde se muestra `como funciona` la herramienta de forma muy resumida y la estética que tiene:

https:// cuando lo suba a youtube lo pongo XD

Y por último y por si acaso, tengo que poner este mensaje:

`[NO ME HAGO RESPONSABLE DEL MAL USO QUE SE LE PUEDA DAR A ESTA HERRAMIENTA O A ESTA INFORMACIÓN]`

<a id="2"></a>
# EXPLICACIÓN TEÓRICA

<a id="WPS"></a>
## Protocolo WPS

EL protocolo `WPS (Wi-Fi Protected Setup)` es un protocolo que `facilita la conexión` de un dispositivo a una red inalambrica (wifi). 

El objetivo de este protocolo es `simplificar el proceso de configuración de seguridad` de una red, lo que permite no tener que memorizar contraseña, y una `conexión mas rápida`. Para esto hay dos formas de conectarse, una forma es `apretando un boton` que suele haber en los routers (aunque a veces esta escondido), de esta forma una vez hemos apretado el boton dependiendo de como esta configurado el `AP (Acces Point)` tines `60 o 120 segundos` para conectarte `sin proporcionar credenciales`. Y la otra forma es proporcionandole un `PIN` de 8 dígitos al AP, claro, esto es mas facil de explotar con fuerza bruta que una contraseña (aunque aún y todo lleva mucho tiempo) por lo que algunas compañías implementan medidas de seguridad como `tras x intentos fallidos bloquear la conexión vía WPS` durante X tiempo o hasta que se reinicie el router, por lo que antes que ponernos a hacer fuerza bruta como si no hubiese un mañana es recomendable probar otras técnicas como pueden ser probar pines con los que vienen `por defecto` los routers o la vulnerabilidad `Pixie Dust`. 

<a id="3"></a>
## Pixie Dust

![](/assets/images/wps_kraken/pixie_dust_diagram.png)

`Pixie Dust` es una técnica que permite explotar una vulnerabilidad en el protocolo `WPS` para obtener el `PIN` de acceso al AP en un `tiempo mucho menor` al necesario con fuerza bruta. Pero hay que aclarar que esta vulnerabilidad `fue corregida en el 2017` por lo que aunque sigue habiendo bastantes redes vulnerables, ya hay muchas que no se pueden explotar con esta técnica.

Esta técnica aprovecha un `defecto en la generación del PIN` de algunos routers que utilizan `chipsets Broadcom` y algunos `Realtek`.

La vulnerabilidad explotada por la técnica de `Pixie Dust` se encuentra en `la forma en que algunos routers generan el PIN` de conexión WPS.

Cuando un dispositivo intenta conectarse a un AP utilizando WPS, `se envía una solicitud` de conexión al AP. El `AP responde` con una petición de `autenticación`, que incluye un `nonce` y un `M1`, que son enviados al dispositivo para que genere un `M2`.

En los routers afectados por la vulnerabilidad, el `M2 se genera de fo`rma predecible, lo que permite a un atacante con acceso al `M1` y al `nonce` `descubrir el PIN` en un corto periodo de tiempo, utilizando técnicas de `fuerza bruta` para encontrar el valor del `M2` correspondiente.

La técnica de Pixie Dust aprovecha precisamente esta vulnerabilidad para obtener el PIN de acceso al AP `en un tiempo mucho menor` al necesario con fuerza bruta.

<a id="SEG"></a>
# MEDIDAS DE SEGURIDAD

Para `prevenir` vulnerabiliades de seguridad relacionadas con el protocolo WPS hay varias medidas que podemos tomar (a parte de las que las compañias de wifis pueden tomar):

La primera y la mas importante es que a menos que sea 100% necesario `DESACTIVA EL WPS`, ya que no hace falta, con la forma tradicional de contraseña (handshake y todo eso) es mas que suficiente y la seguridad es considerablemente superior (aunque la mayoria de protocolos de seguridad (incluso WPA3) tienen vulnerabilidades).

Otra medida de seguridad muy importante que podemos tomar es `mantener actualizados todos los equipos` que tenemos (no solo nuestros ordenadores, moviles... sino que también los routers...), ya que asi si han `parcheado` las vulnerabilidades, podemos solucionar vulnerabilidades como pasa con `Pixie Dust`.

Otra medida en la que no tenemos tanto control pero si podemos mirar compañias que tomen esta medida, es que se pueden `limitar el numero de intentos erroneos de conectarte via WPS`. Así `evitamos ataques de fuerza bruta`. 

Y también es importante mirar que `nuestro router no tenga un PIN WPS genérico`, osea, de fábrica, como pueden ser `("00000000", "11111111", "12345670", "12349876", "98765432", "12345678")` y que sea un PIN aleatorio. 

<a id="4"></a>
# EXPLICACIÓN DEL CÓDIGO Y DEL FUNCIONAMIENTO

En este artículo no voy a explicar línea por línea (o casi) como hice en el de `"Cluedo Cheats"`, ya que lo mas interesante es saber el porque de cada cosa y sinó esto se haría eterno. 

Para eso explicare un poco el funcionamientro, la lógica de ejecución, y luego solamente las funciones:

## FUNCIONAMIENTO



## PARTES


