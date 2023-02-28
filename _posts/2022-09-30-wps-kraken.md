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

Lo primero que necesitamos para que funcione es una `tarjeta de red`, recomiendo la `tplink` porque es la que utilizo (y aunque hace chanel hopping no afecta en el funcionamiento del programa) y solo he testeado la herramienta con eso, pero supongo que se podria utilizar otra (también es importante mencionar que lo he probado en `Parrot Security`, por lo que supongo que en otras distribuciones de linux como `Kali` también funcionara, pero no estoy segura):

![](/assets/images/Wifi-Hacking/TPL-TL-WN722N.jpg)

Ahora la conectamos y al ejecutar el script lo primero que hace es `mostrarnos las interfaces de red disponibles`, como no sabia como automatizar la detección de la interfaz de la tarjeta lo puse de forma manual, por lo tanto lo primero que tenemos que hacer es `seleccionar cual es la interfaz` de red de la tarjeta.

Luego lo que hace es `guardar` la dirección `MAC original`, para que se nos vuelva a poner una vez salgamos del programa sin necesidad de desconectar y volver a conectar la tarjeta, y le `cambia la MAC a una aleatoria`.

Seguidamente ckeckea que todos los programas necesarios esten instalados y si no lo están, los instala automaticamente: `wireshark`, `aircrack-ng`, `wpa_supplicant` 

Y ahora `checkea` que estemos ejecutando el programa en `Linux` y como el usuario `root o con permisos suid` y entra en un bucle que no se parará hasta que se cierre el programa. Dentro de este bucle se repiten las siguientes acciones y opciones:

Primero se muestran todas las redes disponibles.

Una vez las ha mostrado se pone en `modo monitor`.

Y ahora nos da cuatro opciones: `ataques`, `cambiar la mac`, `ayuda` y `salir`.

La primera opción nos da otras dos opciones: `ataques pasivos` y `ataqes activos`:

La primera opción sirve para `intentar conectarte` durante x tiempo cuando quieres que `la víctima le de al boton` o tú o quien sea, para conectarte de forma en la que no tengas que hacer ningún ataque, lo que `minimiza el ruido` que haces pero a la vez es mas complicado de efectuar. 

La segunda opción es mas compleja, es para `conectarte proporcionando el PIN WPS`, para esto dispone de varias fases, la primera es probar si puede obtener el PIN mediante `Pyxye Dust`, si no lo ha logrado prueba con `PINes genericos` que suelen tener varios routers, y por último ya hace `fuerza bruta`. Esto es así para mínimizar el número de intentos, lo que aumenta las posibilidades de éxito. 

La segunda opción del primer menú, nos da la posibilidad de volver a `cambiar la MAC`, por si queresmos seguir haciendo ataques pero para hacer menos ruido preferimos cambiarnos a otra MAC.

La tercera opción no creo que haga falta explicarla, porque basicamente son explicaciones muy por encima de como funciona la herramienta.

Y la última opción es salir, es decir, `cierra el programa de una forma segura`, primero `desactiva el modo monitor`, `vuelve a poner la MAC original` y cierra el programa. 

## PARTES


