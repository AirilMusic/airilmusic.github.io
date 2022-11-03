---
layout: single
title: Cluedo Cheats (Python)
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

Ahora lo que he hecho ha sido crear una `clase: player` y con un `for` meterlo 10 veces en una `lista`. Luego modifico el `atributo: name` para ponerles el nombre de cada jugador y así no tener que memorizar en que posición esta cada uno, sino que puedo filtrarlo por eso o mostrarlos en un menu y luego `instancio las clases en objetos` y los meto en otra lista.

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

Ahora lo que hice fue crear una `función` que `actualize` las `estadisticas y probabilidades` de todos los jugadores.

```py
def updateProbabilitys(): #0 update all, 1 update place, 2 update weaphon, 3 update suspicious
    for i in range(playersNum):
        playerList[i].posiblePlaces = []
        playerList[i].posibleWeaphons = []
        playerList[i].posibleSuspicious = []
       
        if playerList[i].place != "":
            playerList[i].posiblePlaces = [playerList[i].place]
        else:
            for a in range(len(allPlaces)):
                if allPlaces[a] in playerList[i].noCards:
                    pass
                else:
                    playerList[i].posiblePlaces.append(allPlaces[a])

        if playerList[i].weaphon != "":
            playerList[i].posibleWeaphons = [playerList[i].weaphon]
        else:
            for a in range(len(allWeaphons)):
                if allWeaphons[a] in playerList[i].noCards:
                    pass
                else:
                    playerList[i].posibleWeaphons.append(allWeaphons[a])
        
        if playerList[i].suspicious != "":
            playerList[i].posibleSuspicious = [playerList[i].suspicious]
        else:
            for a in range(len(allSuspicious)):
                if allSuspicious[a] in playerList[i].noCards:
                    pass
                else:
                    playerList[i].posibleSuspicious.append(allSuspicious[a])
                    
    for i in range(playersNum):
        if playerList[i].place == "":
            suma1 = 0
            placesProbs = []
            for a in range(len(playerList[i].posiblePlaces)): #calculate probability
                suma = 0.0
                for u in range(playersNum):
                    if playerList[i].posiblePlaces[a] in playerList[u].posiblePlaces:
                        suma += (1/len(playerList[u].posiblePlaces))
                        
                suma = 1 - (suma/playersNum)
                probability = 1/len(playerList[i].posiblePlaces)
                
                if len(playerList[i].noCards) > 0:
                    if probability > suma:
                        probability -= suma
                    if probability < 0:
                        probability = 0.01
                else:
                    probability -= suma
                suma1 += probability
                if suma1 == 0:
                    suma1 = 1
                
                placesProbs.append(probability)
                
            for a in range(len(playerList[i].posiblePlaces)): #round to 0-1
                cardProbability = placesProbs[a]/suma1
                playerList[i].placesProbabilitys.update({playerList[i].posiblePlaces[a] : cardProbability})
        else:
            playerList[i].mostProbablePlace = {playerList[i].place:1}
            allPlaces.remove(playerList[i].place)
            
        if playerList[i].weaphon == "":
            suma2 = 0
            weaphonsProbs = []
            for a in range(len(playerList[i].posibleWeaphons)): #calculate probability
                suma = 0.0
                for u in range(playersNum):
                    if playerList[i].posibleWeaphons[a] in playerList[u].posibleWeaphons:
                        suma += (1/len(playerList[u].posibleWeaphons))
                        
                suma = 1 - (suma/playersNum)
                probability = 1/len(playerList[i].posibleWeaphons)
                
                if len(playerList[i].noCards) > 0:
                    if probability > suma:
                        probability -= suma
                    if probability < 0:
                        probability = 0.01
                else:
                    probability -= suma
                suma2 += probability
                if suma2 == 0:
                    suma2 = 1
                
                weaphonsProbs.append(probability)
            
            for a in range(len(playerList[i].posibleWeaphons)): #round to 0-1
                cardProbability = weaphonsProbs[a]/suma2
                playerList[i].weaphonsProbabilitys.update({playerList[i].posibleWeaphons[a] : cardProbability})
        else:
            playerList[i].mostProbableWeaphon = {playerList[i].weaphon:1}
            allWeaphons.remove(playerList[i].weaphon)
            
        if playerList[i].suspicious == "":
            suma3 = 0
            suspiciousProbs = []
            for a in range(len(playerList[i].posibleSuspicious)): #calculate probability
                suma = 0.0
                for u in range(playersNum):
                    if playerList[i].posibleSuspicious[a] in playerList[u].posibleSuspicious:
                        suma += (1/len(playerList[u].posibleSuspicious))
                        
                suma = 1 - (suma/playersNum)
                probability = 1/len(playerList[i].posibleSuspicious)
                
                if len(playerList[i].noCards) > 0:
                    if probability > suma:
                        probability -= suma
                    if probability < 0:
                        probability = 0.01
                else:
                    probability -= suma
                suma3 += probability
                if suma3 == 0:
                    suma3 = 1
                
                suspiciousProbs.append(probability)
            
            for a in range(len(playerList[i].posibleSuspicious)): #round to 0-1
                cardProbability = suspiciousProbs[a]/suma3
                playerList[i].suspiciousProbabilitys.update({playerList[i].posibleSuspicious[a] : cardProbability})
        else:
            playerList[i].mostProbableSuspicious = {playerList[i].suspicious:1}
            allSuspicious.remove(playerList[i].suspicious)
                
        if playerList[i].place == "":
            placeKey = max(playerList[i].placesProbabilitys)
            placeValue = playerList[i].placesProbabilitys[placeKey]
            playerList[i].mostProbablePlace = {placeKey:placeValue}
        
        if playerList[i].weaphon == "":
            weaphonKey = max(playerList[i].weaphonsProbabilitys)
            weaphonValue = playerList[i].weaphonsProbabilitys[weaphonKey]
            playerList[i].mostProbableWeaphon = {weaphonKey:weaphonValue}
        
        if playerList[i].suspicious == "":
            suspiciousKey = max(playerList[i].suspiciousProbabilitys)
            suspiciousValue = playerList[i].suspiciousProbabilitys[suspiciousKey]
            playerList[i].mostProbableSuspicious = {suspiciousKey:suspiciousValue}

updateProbabilitys()
print("")
```

### EXPLICACION DETALLAD DE LA FUNCIÓN

```py
def updateProbabilitys():
```

Lo primero que hace la función es `resetear` las posibles cartas que tiene cada jugador a 0:

```py
        for i in range(playersNum):
                playerList[i].posiblePlaces = [] #reset
                playerList[i].posibleWeaphons = []
                playerList[i].posibleSuspicious = []
```

Luego `reduce el numero de posibles cartas` y las añade a las listas que ha reseteado, para eso lo que hace es primero `mirar si hay alguna carta que sabemos que tiene el jugador` y `si no sabemos que carta tiene` (del lugar, arma o sospechoso) mira si las cartas de las listas de todas las cartas estan en la lista de cartas que no tiene ese jugador `player[i].noCards` y `si no estan` en esa lista `las añade a las listas de posibles cartas` de ese jugador:

```py
        if playerList[i].place != "":
            playerList[i].posiblePlaces = [playerList[i].place]
        else:
            for a in range(len(allPlaces)):
                if allPlaces[a] in playerList[i].noCards:
                    pass
                else:
                    playerList[i].posiblePlaces.append(allPlaces[a])

        if playerList[i].weaphon != "":
            playerList[i].posibleWeaphons = [playerList[i].weaphon]
        else:
            for a in range(len(allWeaphons)):
                if allWeaphons[a] in playerList[i].noCards:
                    pass
                else:
                    playerList[i].posibleWeaphons.append(allWeaphons[a])
        
        if playerList[i].suspicious != "":
            playerList[i].posibleSuspicious = [playerList[i].suspicious]
        else:
            for a in range(len(allSuspicious)):
                if allSuspicious[a] in playerList[i].noCards:
                    pass
                else:
                    playerList[i].posibleSuspicious.append(allSuspicious[a])
```

Una vez ya tenemos la lista de posibles cartas ya podemos ver la `probabilidad` de que cada jugador tenga x carta. Claro, hay un problema, si lo que hacemos es `1/posiblesCartas` todas las cartas (bueno, de cada grupo: lugar/arma/sospechoso) `tendran la misma probabilidad`. Pero eso no es del todo verdad, ya que podemos saber que `una carta tiene mas probabilidad de que la tenga otro jugador`. Claro, si lo pasamos a un arbol de probabilidades sabemos las probabilidades de las ramas del fina, pero nos faltan las primeras ramas:

![](/assets/images/Algoritmia/arbol-probs.png)

Por lo que `podríamos comparar las probabilidades` de que una carta salga `en otros jugadores` y segun eso luego sacar las probabilidades de que un jugador tenga x carta. Creo que no se entiende así que lo explicaré un poquito mejor. Todas las cartas del mismo grupo de un jugador tienen `la misma probabilidad` (por ejemplo 0,25), entonces para lo que nos sirve todas son iguales. Así que ahora hacemos la `suma` de que cada carta de esas salga en todos los jugadores y lo `dividimos` entre el `numero maximo` que nos podría salir, es decir, el `numero de jugadores`. Con esto nos da una `probabilidad`, y hora eso lo tenemos que pasar a la probabilidad que ya tenemos de esa carta y `compararla` con lo mismo de las otras cartas. Para eso lo que he hecho es `restarle` a la probabilidad original el contrario de la probabilidad que hemos conseguido con la suma. Así las probabilidades del inicio que eran iguales ahora `son distintas`, pero ya `no son de 0-1`, por lo que hay que `volver a pasarlas` a esa "escala". Para eso he hecho lo siguiente:  `probabilidad/sumaDeProbabilidades` (esas probabilidades son el resultado de la resta y la suma es la suma de todas esas)

(pequeña aclaración, eso que he dicho solo se hace `si no sabemos la carta que tiene`, sino directamente la carta es la que sabemos y la probabilidad es 1)

```py
    for i in range(playersNum):
        if playerList[i].place == "":
            suma1 = 0
            placesProbs = []
            for a in range(len(playerList[i].posiblePlaces)): #calculate probability
                suma = 0.0
                for u in range(playersNum):
                    if playerList[i].posiblePlaces[a] in playerList[u].posiblePlaces:
                        suma += (1/len(playerList[u].posiblePlaces))
                        
                suma = 1 - (suma/playersNum)
                probability = 1/len(playerList[i].posiblePlaces)
                
                if len(playerList[i].noCards) > 0:
                    if probability > suma:
                        probability -= suma
                    if probability < 0:
                        probability = 0.01
                else:
                    probability -= suma
                suma1 += probability
                if suma1 == 0:
                    suma1 = 1
                
                placesProbs.append(probability)
                
            for a in range(len(playerList[i].posiblePlaces)): #round to 0-1
                cardProbability = placesProbs[a]/suma1
                playerList[i].placesProbabilitys.update({playerList[i].posiblePlaces[a] : cardProbability})
        else:
            playerList[i].mostProbablePlace = {playerList[i].place:1}
            allPlaces.remove(playerList[i].place)
            
        if playerList[i].weaphon == "":
            suma2 = 0
            weaphonsProbs = []
            for a in range(len(playerList[i].posibleWeaphons)): #calculate probability
                suma = 0.0
                for u in range(playersNum):
                    if playerList[i].posibleWeaphons[a] in playerList[u].posibleWeaphons:
                        suma += (1/len(playerList[u].posibleWeaphons))
                        
                suma = 1 - (suma/playersNum)
                probability = 1/len(playerList[i].posibleWeaphons)
                
                if len(playerList[i].noCards) > 0:
                    if probability > suma:
                        probability -= suma
                    if probability < 0:
                        probability = 0.01
                else:
                    probability -= suma
                suma2 += probability
                if suma2 == 0:
                    suma2 = 1
                
                weaphonsProbs.append(probability)
            
            for a in range(len(playerList[i].posibleWeaphons)): #round to 0-1
                cardProbability = weaphonsProbs[a]/suma2
                playerList[i].weaphonsProbabilitys.update({playerList[i].posibleWeaphons[a] : cardProbability})
        else:
            playerList[i].mostProbableWeaphon = {playerList[i].weaphon:1}
            allWeaphons.remove(playerList[i].weaphon)
            
        if playerList[i].suspicious == "":
            suma3 = 0
            suspiciousProbs = []
            for a in range(len(playerList[i].posibleSuspicious)): #calculate probability
                suma = 0.0
                for u in range(playersNum):
                    if playerList[i].posibleSuspicious[a] in playerList[u].posibleSuspicious:
                        suma += (1/len(playerList[u].posibleSuspicious))
                        
                suma = 1 - (suma/playersNum)
                probability = 1/len(playerList[i].posibleSuspicious)
                
                if len(playerList[i].noCards) > 0:
                    if probability > suma:
                        probability -= suma
                    if probability < 0:
                        probability = 0.01
                else:
                    probability -= suma
                suma3 += probability
                if suma3 == 0:
                    suma3 = 1
                
                suspiciousProbs.append(probability)
            
            for a in range(len(playerList[i].posibleSuspicious)): #round to 0-1
                cardProbability = suspiciousProbs[a]/suma3
                playerList[i].suspiciousProbabilitys.update({playerList[i].posibleSuspicious[a] : cardProbability})
        else:
            playerList[i].mostProbableSuspicious = {playerList[i].suspicious:1}
            allSuspicious.remove(playerList[i].suspicious)
```

Y ya por último vemos que probabilidad es `mayor` y la guardamos con su respectiva carta:

```py
        if playerList[i].place == "":
            placeKey = max(playerList[i].placesProbabilitys)
            placeValue = playerList[i].placesProbabilitys[placeKey]
            playerList[i].mostProbablePlace = {placeKey:placeValue}
        
        if playerList[i].weaphon == "":
            weaphonKey = max(playerList[i].weaphonsProbabilitys)
            weaphonValue = playerList[i].weaphonsProbabilitys[weaphonKey]
            playerList[i].mostProbableWeaphon = {weaphonKey:weaphonValue}
        
        if playerList[i].suspicious == "":
            suspiciousKey = max(playerList[i].suspiciousProbabilitys)
            suspiciousValue = playerList[i].suspiciousProbabilitys[suspiciousKey]
            playerList[i].mostProbableSuspicious = {suspiciousKey:suspiciousValue}
```

Y luego `llamo a esta función` para que al principio todas las `probabilidades` de los jugadores esten a `0` o a la `probabilidad del principio`:

```py
updateProbabilitys() # this is for set all stadistics to 0 or initial probabilitys...
print("")
```

### FIN DE LA EXPLICACIÓN DETALLADA DE LA FUNCIÓN







