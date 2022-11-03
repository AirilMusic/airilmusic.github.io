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

Ahora lo que he hecho ha sido crear un `objeto: player` y con un for meterlo 10 veces en una `lista`. Luego modifico el `atributo: name` para ponerles el nombre de cada jugador y así no tener que memorizar en que posición esta cada uno, sino que puedo filtrarlo por eso o mostrarlos en un menu.

```py
p = []

for i in range(10):
    class player():
        name = str(i)
        
        place = ""
        weaphon = ""
        suspicious = ""
        
        noCards = [] #cartas que no tiene el jugador
        
        posiblePlaces = []
        posibleWeaphons = []
        posibleSuspicious = []
        
        placesProbabilitys = {}
        weaphonsProbabilitys = {}
        suspiciousProbabilitys = {}
        
        mostProbablePlace = {"" : 0.0}
        mostProbableWeaphon = {"" : 0.0}
        mostProbableSuspicious = {"" : 0.0}
    
    p.append(player)
    
    for a in range (len(allPlaces) - 1):
        p[i].placesProbabilitys.update({allPlaces[a]:0.0})
    for a in range (len(allWeaphons) - 1):
        p[i].weaphonsProbabilitys.update({allWeaphons[a]:0.0})
    for a in range (len(allSuspicious) - 1):
        p[i].suspiciousProbabilitys.update({allSuspicious[a]:0.0})

player1 = p[0]
player2 = p[1]
player3 = p[2]
player4 = p[3]
player5 = p[4]
player6 = p[5]
player7 = p[6]
player8 = p[7]
player9 = p[8]
player10 = p[9]

print("")

while True:
    playersNumT = input("How many players are playing (Max: 10): ")
    if playersNumT.isnumeric():
        if int(playersNumT) < 10 and int(playersNumT) >= 1:
            playersNum = int(playersNumT)
            break
noPlaying = 10-playersNum

names = []
for i in range(playersNum):
    question = "Name of the player" + str(i + 1) + ": "
    names.append(str(input(question)).lower())

for i in range(noPlaying):
    names.append("0")
    
player1.name = names[0]
player2.name = names[1]
player3.name = names[2]
player4.name = names[3]
player5.name = names[4]
player6.name = names[5]
player7.name = names[6]
player8.name = names[7]
player9.name = names[8]
player10.name = names[9]

playerList = (player1, player2, player3, player4, player5, player6, player8, player9, player10)
```
