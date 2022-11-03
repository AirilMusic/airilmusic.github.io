---
layout: single
title: Cluedo Cheats Python
excerpt: "Explicación de un programa que he hecho que dice las probabilidades de cada persona en el Cluedo, las mejores preguntas..."
date: 2022-11-03
classes: wide
header:
  teaser: /assets/images/Algoritmia/cluedo-portada.png
  teaser_home_page: true
  icon: 
categories:
  - Programacion
tags:  
  - Python
---

![](/assets/images/Algoritmia/cluedo-portada.png)

# CLUEDO CHEATS

`Código y .exe:` https://github.com/AirilMusic/Programitas/tree/main/cluedo_cheats_uwu

En verano en una casa rural con amigos jugamos al cluedo aunque nosotros eramos las piezas y el tablero era la casa rural. Entonces se me ocurrió hacer ete programa, lo empecé a programar desde el móvil, pero como empezaba a quedar enorme no lo seguí, así que hace unos días me propuse rehacer este proyecto y ha salido bastante bien.

Esto es un programa que he hecho que dice las `probabilidades` de que cada jugador tenga x carta, tambien te dice la carta mas probabable que tenga, las `mejores preguntas` que puedes hacer y el `asesino, escena del crimen y arma mas probables`.

## ÍNDICE

```
  - Introducción
  - Índice
  - Explicación teórica y de código
  - Exportar a .exe
```

## EXPLICACIÓN TEÓRICA Y DE CÓDIGO

Lo primero importamos la libreria `time` para que al final espere un poquito:

```py
import time
```

Luego pedimos que el usuario meta las `cartas` del cluedo que se estan utilizando, primero las posibles escenas del crimen, luego las armas y por último los sospechosos (con el `while` hago que mientras no ponga como `input: exit` seguira metiendo cartas de forma indefinida) y esas cartas las metemos en unas `listas (arrays)` en las que se almacenarán:

```py
# CARDS
allPlaces = []
allWeaphons = []
allSuspicious = []

while True:
    add = input("Add a place (exit if there is no more): ")
    if add.lower() == "exit":
        break
    else:
        allPlaces.append(add.lower())

print("")

while True:
    add = input("Add a weaphon (exit if there is no more): ")
    if add.lower() == "exit":
        break
    else:
        allWeaphons.append(add.lower())

print("")

while True:
    add = input("Add a suspicious (exit if there is no more): ")
    if add.lower() == "exit":
        break
    else:
        allSuspicious.append(add.lower())
```
