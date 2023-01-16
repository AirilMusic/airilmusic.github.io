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

# PRIMEROS X CARACTERES

Para mostrar los primeros x caracteres de un string o un número (para string hay que hacer un par de pequeños cambios):

```py
num = str(n)
nums = []

for i in num:
    nums.append(i)
    
print(int(str(nums[0] + nums[1] + nums[2] + nums[3] + nums[4] + nums[5] + nums[6] + nums[7] + nums[8] + nums[9])))
```

# RELLENAR DE 00000
Cuando las variables que utilizamos necesitan ser de la misma longitud, por ejemplo en binario, si tenemos un número con una cantidad de dígitos menor al que necesitamos tenemos que ponerle antes `0000`, es decir, si tenemos `1001` y necesitamos que tenga 8 caracteres, lo tendremos que poner así `00001001`:

```py
numeroSin0 = input("El numero:")         # por ejemplo '1001' 

OtoAdd = int(8 - len(numeroSin0))        # con esto calculamos cuantos 0 nos faltan

pin = str(numeroSin0.zfill(OtoAdd))      # con esto ñadimos esos 0, dejandolo en 00001001
```


