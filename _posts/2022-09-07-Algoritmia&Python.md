---
layout: single
title: Apuntes algoritmia & python
excerpt: "Visto que en 5 meses más o menos hay de nuevo Olimpiadas informáticas de nivel regional con las que te puedes clasificar para estatal (y obviamente si puedo iré por segunda vez). Se me ha ocurrido poner aquí apuntes de algoritmos que me resultan interesantes y útiles."
date: 2022-09-07
classes: wide
header:
  teaser: /assets/images/Algoritmia/portada.jpg
  teaser_home_page: true
  icon: 
categories:
  - Programacion
tags:  
  - Python
---

(según valla utilizando nuevos algoritmos en ejercicios iré añadiendo mas aquí)

# ÍNDICE
- [SUMATORIOS](#1)
- [NUMEROS PRIMOS](#2)
- [PRIMEROS X CARACTERES](#3)
- [RELLENAR DE 0000](#4)
- [QUE TIPO DE VARIABLE ES](#5)
- [BUSQUEDA BINARIA](#6)
- [ORDENAMIENTO](#7)
- [DIJKSTRA y A*](#8)
- [BACKTRACKING](#9)
- [TÉCNICAS DE RESOLUCIÓN DE PROBLEMAS](#10)

<a id="1"></a>
# SUMATORIOS

(también son útiles para números triangulares)

```py
1 + 2 + 3 + ... + n                    -->              x = (n * (n + 1))/2
1^2 + 2^2 + 3^2 + ... + n^2            -->              x = (n * (n + 1) * (2 * n + 1))/6
1^3 + 2^3 + 3^3 + ... + n^3            -->              x = ((n**2) * ((n + 1)**2))/4
1^4 + 2^4 + 3^4 + ... + n^4            -->              x = (n * (n + 1) * (2 * n + 1) *(3 * (n**2) + 3 * n - 1))/30
1^5 + 2^5 + 3^5 + ... + n^5            -->              x = ((n **2) * ((n + 1)**2) * (2 * (n**2) + 2 * n -1))/12
1^6 + 2^6 + 3^6 + ... + n^6            -->              x = (n * (n + 1) * (2 * n + 1) * (3 * (n**4) + 6 * (n**3) - 3n + 1))/42
```

<a id="2"></a>
# LISTAR NUMEROS PRIMOS

## Forma lógica:

```py
numMax == 10000 #el numero maximo que se quiere analizar, es decir, que se listaran los primos desde el numero 2 hasta ese
prime = [2, ]
for i in range(3, numMax + 1):
	     posPrimo= True
 
	    for uwu in range(2, int(i / 2)):
		    if i % uwu == 0:
			    posPrimo = False
			    break
 
	    if posPrimo:
		    prime.append(i)
 
print(prime)
```

<a id="3"></a>
# PRIMEROS X CARACTERES

Para mostrar los primeros x caracteres de un string o un número (para string hay que hacer un par de pequeños cambios):

```py
num = str(n)
nums = []

for i in num:
    nums.append(i)
    
print(int(str(nums[0] + nums[1] + nums[2] + nums[3] + nums[4] + nums[5] + nums[6] + nums[7] + nums[8] + nums[9])))
```

<a id="4"></a>
# RELLENAR DE 00000
Cuando las variables que utilizamos necesitan ser de la misma longitud, por ejemplo en binario, si tenemos un número con una cantidad de dígitos menor al que necesitamos tenemos que ponerle antes `0000`, es decir, si tenemos `1001` y necesitamos que tenga 8 caracteres, lo tendremos que poner así `00001001`:

```py
numeroSin0 = input("El numero:")         # por ejemplo '1001' 

OtoAdd = int(8 - len(numeroSin0))        # con esto calculamos cuantos 0 nos faltan

pin = str(numeroSin0.zfill(OtoAdd))      # con esto ñadimos esos 0, dejandolo en 00001001
```

<a id="5"></a>
# VER QUE TIPO DE VARIABLE ES UNA VARIABLE
Si tenemos por ejemplo una raiz cuadrada y queremos ver si el output es un número entero necesitamos algun función con la que poder comprobarlo. Realmente esto se puede hacer para todo tipo de variables, pero lo voy a hacer con el ejemplo que he puesto. Para esto utilizaremos la función `isinstance(x, int)`:

```py
x = 4 ** 0.5
print(isinstance(x, int)) # imprime True porque la raiz cuadrada de 4 es 2, osea un número entero

x = 5 ** 0.5
print(isinstance(x, int)) # imprimer False porque la raiz cuadrada de 5 es 2,236... 
```

<a id="6"></a>
# BUSQUEDA BINARIA

El algoritmo de búsqueda binaria es un algoritmo de `búsqueda eficiente` para encontrar un elemento específico en una `lista ordenada`. Funciona `dividiendo la lista en dos partes` cada vez, comparando el elemento buscado con el elemento del medio de la lista y `eliminando la mitad` de la lista en la que el elemento no puede estar.

En el siguiente ejemplo a una función le metemos una lista `arr` y el objeto que queremos buscar `x`:

```py
def binary_search(arr, x):
    low = 0
    high = len(arr) - 1
    mid = 0

    while low <= high:
        mid = (high + low) // 2

        # si x es mayor, ignora el lado izquierdo
        if arr[mid] < x:
            low = mid + 1

        # si x es menor, ignora el lado derecho
        elif arr[mid] > x:
            high = mid - 1

        # si x esta en el medio
        else:
            return(mid)

    # If we reach here, then the element was not present
    return("El elemento no esta en la lista")
```

<a id="7"></a>
# ORDENAMIENTO

Hay varios algoritmos de ordenamiento, como `bubble sort`, `insertion sort`, `merge sort`, `quick sort`, entre otros. Es importante conocer al menos uno o dos de estos algoritmos y saber cómo implementarlos en Python.

## BUUBLE SORT

Es un algoritmo de ordenamiento simple que repite a través de una lista de elementos varias veces, `comparando elementos adyacentes` y `intercambiándolos si están en el orden equivocado`. El proceso se repite hasta que no se realizan más intercambios, lo que indica que la lista está ordenada.

En el siguiente ejemplo, la función `bubble_sort()` toma una `lista desordenada` como input y `la devuelver ordenada`:

```py
def bubble_sort(lista):
    n = len(lista)
    for i in range(n):
        for j in range(0, n-i-1):
            if lista[j] > lista[j+1]:
                lista[j], lista[j+1] = lista[j+1], lista[j]
    return(lista)
```

## INSERTION SORT

El algoritmo comienza con una `lista vacía` en la cual se va `insertando cada elemento de la lista original` en orden, `comparando y colocando cada elemento en su posición correcta en la lista ordenada`. El proceso `se repite hasta que todos los elementos del arreglo original hayan sido insertados` en la lista ordenada. El tiempo de ejecución del algoritmo es `O(n^2) en el peor caso`, pero `puede ser muy eficiente` en caso de que `la lista ya esté parcialmente ordenada`.

```py
def insertion_sort(arr):
    # Recorremos todos los elementos del arreglo
    for i in range(1, len(arr)):
        # Asignamos el valor de la posición actual
        key_item = arr[i]
        # Asignamos el índice del elemento anterior
        j = i-1
        # Mientras el elemento anterior sea mayor que el elemento actual y el índice sea mayor o igual a cero
        while j >=0 and key_item < arr[j] :
                arr[j + 1] = arr[j]
                j -= 1
        # Colocamos el elemento actual en su posición correcta
        arr[j + 1] = key_item
    return(arr)
```

## MERGE SORT

Merge sort es un algoritmo de ordenamiento basado en la técnica de `dividir y conquistar`. El algoritmo divide una lista en `dos mitades`, ordena cada mitad de forma independiente y luego combina las dos mitades ordenadas en una sola lista ordenada. El proceso de dividir se repite recursivamente `hasta que cada mitad tenga solo un elemento`, lo que significa que está ordenado. A continuación, `se combinan las mitades` ordenadas utilizando un proceso conocido como `merge`, en el cual `se comparan los elementos de ambas mitades` y se colocan en orden en la lista final. El algoritmo merge sort es conocido por ser estable y tener un tiempo de ejecución de `O(n log n)`.

```py
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left_half = merge_sort(arr[:mid])
    right_half = merge_sort(arr[mid:])
    return merge(left_half, right_half)

def merge(left, right):
    result = []
    i = 0
    j = 0
    while i < len(left) and j < len(right):
        if left[i] < right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result += left[i:]
    result += right[j:]
    return result

arr = [38, 27, 43, 3, 9, 82, 10]
print(merge_sort(arr))
# Output: [3, 9, 10, 27, 38, 43, 82]
```

## QUICK SORT

Este es otro algoritmo de ordenamiento de `divide y vencerás`. Funciona dividiendo una lista en `dos sublistas`, `una con elementos menores que` un `pivote` y `otra con elementos mayores`. Luego, `se ordenan recursivamente` las dos sublistas y `se combinan` para obtener la lista ordenada completa. El `pivote se elige` comúnmente como el `primer`, `último` o elemento `medio` de la lista. Quick sort es un algoritmo eficiente en términos de tiempo, con un tiempo de ejecución promedio de `O(n log n)`. Sin embargo, su desempeño puede verse afectado por la elección del pivote y puede ser menos eficiente en caso de listas ya ordenadas o con muchos elementos repetidos.

```py
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return(quick_sort(left) + middle + quick_sort(right))
```

<a id="8"></a>
# DIJKSTRA y A*

Son algoritmos de búsqueda de rutas que se utilizan para encontrar la ruta más corta entre dos puntos en un grafo.



<a id="9"></a>
# BACKTRACKING

Es un algoritmo de búsqueda que se utiliza para encontrar todas las soluciones posibles a un problema.



<a id="10"></a>
# TÉCNICAS DE RESOLUCIÓN DE PROBLEMAS
## DIVIDE Y VENCERÁS

Es una técnica de resolución de problemas en la que un problema grande se divide en problemas más pequeños y más fáciles de resolver.

## PROGRAMACIÓN DINÁMICA

Es una técnica de resolución de problemas que consiste en dividir un problema en subproblemas y almacenar las soluciones a estos subproblemas para evitar tener que volver a resolverlos.

Es muy útil utilizar funciones para esto.

## GREEDY

Es una técnica de resolución de problemas en la que se toma la decisión óptima en cada paso con la esperanza de que esto lleve a una solución global óptima.
