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

```
import tensorflow as tf

import numpy as np
```

Ahora crearemos los `datasets`, para esto vamos a utilizar datos del histórico del precio del Bitcoin:

```
day = np.array([1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83], dtype=int)

price = np.array([20386.6, 20444.6, 18986.5, 20577.2, 20572.3, 20720.4, 19965.8, 21100.7, 21226.9, 21489.9, 21043.5, 20730.2, 20278.0, 20111.3, 19926.6, 19262.9, 19243.2, 19309.9, 20215.8, 20200.6, 20561.1, 21637.8, 21611.2, 21587.5, 20847.4, 19963.2, 19330.9, 20250.0, 20586.0, 20825.1, 21209.9, 20785.6, 22525.8, 23410.2, 23215.2, 23153.0, 22675.2, 22460.4, 22582.1, 21301.9, 21248.7, 22958.3, 23850.0, 23774.3, 23634.2, 23303.4, 23271.2, 22988.6, 22820.8, 22612.1, 23308.2, 22944.2, 23175.3, 23816.3, 23146.7, 23962.9, 23935.3, 24398.7, 24442.5, 24302.8, 24101.7, 23856.8, 23338.0, 23203.6, 20831.3, 21138.9, 21517.2, 21416.3, 21517.2, 21365.2, 21565.4, 20249.9, 20033.9, 19550.2, 20295.9, 19808.5, 20043.9, 20126.1, 19951.7, 19736.2, 19999.9, 19793.1, 18786.4], dtype=float)
```

En caso de una IA bien hecha ahora toca poner un `pequeño dataset` con el cual la red neuronal `no se entrenará`, pero servira para `examinarla`. De esta forma nos quitaríamos el problema de que pueda haber `overfiting` o `underfiting` es decir, que o `memorice` los datos en vez de aprender de ellos, o que `no sea precisa`. (Pero en esta red decidí no usarlo para no complicarlo mucho) 

```
trainDay = np.array([7,16,22,41,60], dtype=int)

trainPrice = np.array([19965.8, 19262.9, 21637.8, 21248.7, 24302.8], dtype=int)
```

Ahora toca crear la `estructura`, para esto vamos a utilizar la estructura que `ya he explicado antes`:

```
model = tf.keras.Sequential([

    tf.keras.layers.Flatten(input_shape=[1]),
    
    tf.keras.layers.Dense(1000, activation = tf.nn.relu), 
    
    tf.keras.layers.Dense(1500, activation = tf.nn.relu),
    
    tf.keras.layers.Dense(1500, activation = tf.nn.relu),
    
    tf.keras.layers.Dense(1500, activation = tf.nn.relu),
    
    tf.keras.layers.Dense(1500, activation = tf.nn.relu),
    
    tf.keras.layers.Dense(1000, activation = tf.nn.relu),
    
    tf.keras.layers.Dense(1)

])
```

Ahora vamos a compilarlo (al ser simple nos es suficiente con el optimizador `Adam` y la `función de error` es `mean_squared_error` para que aprenda de una forma óptima):

```
model.compile(optimizer = tf.keras.optimizers.Adam(0.1), loss = 'mean_squared_error')
```

Para `entrenar` la red neuronal tendremos que utilizar la función `model.fit()` de la siguiente manera: `model.fit(datasetA, datasetB, epoch = número_de_vueltas, vervose = False)`. El `número de vueltas` es las veces que la red neuronal repasará los datasets completos para entrenarse, y ponemos `vervose = False` para que no nos ponga mucha información innecesaria por consola, pero si queremos ver por ejemplo en que vuelta va podemos ponerlo en True.

```
print("\nStarting training...")

historyR = model.fit(day, price, epochs = 2500, verbose=False)

print("Trainning ended uwu")
```

Por último ya podemos hacer `predicciones`:

```
print("Predicting...")

newday = int(input("Day (next to 2022-06-16, example: 63): "))

resultado = model.predict([newday])

print("Es probable que el precio ronde los: ", resultado, " $\n\n\n\n") 
```

# PROBLEMAS Y SOLUCIONES

El problema más interesante y más curioso (por eso lo pongo el primero) es que `no siempre es mejor tener muchos datos`, es decir, bitcoin puede subir y bajar por acciones inesperadas (que un famoso apoye o este en contra del bitcoin...) por lo tanto, hay veces que la IA se vuelve loca cuando cae el precio de golpe. Para esto lo que he hecho es poner un límite de datos máximos en el dataset, es decir, los datos óptimos son desde entre 10 y 30 días atras.

El segundo problema más común en muchas IA-s y que me ha dado algún problemita es que `los datasets son muy sensibles` es decir, que al mínimo fallo peta.

Algo parecido también pasa con el número de capas, por ejemplo, no sé porque, pero `si pongo una capa más también peta` y todavía no sé porque pasa.

Y otro problema que puede haber es uno que he mencionado anteriormente, es decir: `las neuronas muertas`. Para esto hay otras funciones de activación que no se quedan en 0 como por ejemplo `Elu`, `LeakyReLU`...

# RESULTADO

Al principio las primeras predicciones eran muy buenas, pero no tuve en cuenta porque todavía no conocía ese problema y era porque apenas había 30 días de datos.

![](/assets/images/Bitcoin-AI/resultado1.PNG)

![](/assets/images/Bitcoin-AI/resultado2.PNG)

Luego a medida que fuí metiendo mas datos fue empeorando, y termino de empeorar cuando de repente bajo el precio de bitcoin bastante.

![](/assets/images/Bitcoin-AI/resultado3.PNG)

Y luego al hacer pruebas me di cuenta de que el número óptimo de días anteriores para el entrenamiento era entre 10 y 30 y la mejora es abrumadora.

![](/assets/images/Bitcoin-AI/resultado4.PNG)

Y pues así va, mi intención es hacerle todavía más mejoras, y si veo que va bien, pues quizás la publico en algún lado.

