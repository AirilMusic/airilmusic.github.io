---
layout: single
title: Bitcoin AI
excerpt: "Este es el primer proyecto que he hecho con redes neuronales y trata de predecir el precio del bitcoin del día siguiente y de dentro de dos."
date: 2022-09-09
classes: wide
header:
  teaser: /assets/images/Bitcoin-AI/Diagrama.png
  teaser_home_page: true
  icon: 
categories:
  - AI
tags:  
  - Python
  - Tensorflow
  - Virtual Network
---

Este es el primer proyecto que he hecho con redes neuronales y trata de predecir el precio del bitcoin del día siguiente y de dentro de dos.

Esto se me ocurrió porque es un proyecto `"simple"` para empezar a poner en práctica lo que voy aprendiendo sobre redes neuronales. Básicamente debería funcionar con la gráfica del precio del bitcoin, pero al ponerlo en práctica no es tan simple.

## ÍNDICE

```
  -Explicación simple (teórica)
  
  -Explicación técnica (código)
  
  -Problemas y Soluciones
  
  -Resultado
``` 

# EXPLICACIÓN

Al ser `mi primera red neuronal` es muy `simple`. Básicamente es una red neuronal simple con un único `input (el día)` y con un único `output (el precio del bitcoin en dólares)`. Y cogiendo de `referencia los días anteriores` aprende el comportamiento del bitcoin y es capaz de `predecir` el de los siguientes dos días.

## ESTRUCTURA

![](/assets/images/Bitcoin-AI/Diagrama.png)

Como he puesto en el diagrama, la red neuronal está compuesta de `8 capas`. 

La primera es el `input` y la última es el `output` (el output utiliza la `función de activación: relu`, ya que es simple y lo que necesito es un número concreto).

Las seis del medio son `capas ocultas`, es decir, son capas que hay entre el input y el output que se encargan de `realizar cálculos`. Todas son `capas densas` es decir, que cada neurona de una capa está conectada con todas las neuronas de la capa anterior y de la siguiente. (El número de capas y de neuronas lo fui probando hasta que di con el que mejores resultados daba). Y la `función de activación` también es `relu`, ya que aunque da la posibilidad de que haya `neuronas muertas` es decir (neuronas que no se usan) es más `eficiente` en el tema de tiempo y de capacidad de computación que sus alternativas.

# EXPLICACIÓN TÉCNICA:

Para esto vamos a utilizar la librería `Tensorflow`, que es una `librería de Python` que facilita mucho el trabajo con redes neuronales. Y también vamos a utilizar la librería `NumPy` para que el `dataset` este hecho por `matrices`.



