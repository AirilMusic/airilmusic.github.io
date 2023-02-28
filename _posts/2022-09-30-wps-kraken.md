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
- [PIXIE DUST](#3)
- [EXPLICACIÓN DEL CÓDIGO](#4)

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

# EXPLICACIÓN TEÓRICA
