---
layout: single
title: Wifi Hacking 2, preparación antes de atacar
excerpt: "Para efectuar un buen ataque y mantenernos en el anonimato primero debemos ocultar nuestro rastro. Y también tendremos que ver que redes hay e información de las mismas."
date: 2022-07-04
classes: wide
header:
  teaser: 
  teaser_home_page: true
  icon:
categories:
  - Wifi-Hacking
tags:  
  - Wifi-Hacking
---

En este segundo artículo primero veremos como cambiar nuestra MAC para que no se nos pueda identificar al llevar a cabo los ataques. Y luego ya empezaremos a hacer cositas, primero vamos a ver las redes que están a nuestro alcance y diversa información útil de las mismas.


## FALSIFICAR NUESTRA MAC

Es importante hacer esto antes de efectuar cualquier ataque a una red, ya que si no lo hacemos la compañía o cualquiera que este snifanzo la red puede ver los paquetes que mandemos y ahí podría ver nuestra MAC, lo que nos delataría. Para que esto no suceda vamos a cambiar nuestra MAC: (es necesario estar logueados como root para cambiar nuestra MAC)
```
> macchanger {tarjeta de red}               #(para ver la direccion mac)
(en una direccion mac, los tres primeros numeros identifican la tecnologia del dispositivo)

> macchanger -l                             #(para ver muchas mac)    ESTO SE PUEDE SALTAR

> macchanger -l | grep "NATIONAL SECURITY AGENCY"           #(para filtral las MAC por las de otra empresa/organicacion, en este caso la NSA)

> macchanger --mac={los primeros 3 pares de numeros de lo que hayas filtrado}{los siguientes 3 pares se pueden inventar} {el nombre de la tarjeta en modo monitor}

> ifconfig {tarjeta} down                   #(para darla de baja)

> macchanger -s {tarjeta}                   #(para ver la mac nueva)
```

Ejemplo:

![](/assets/images/Wifi-Hacking/cambiando_mac.PNG)

Y así de fácil, literalmente o lo automatizas o por memoria muscular lo haces en unos pocos segundos.

(no sé si es necesario aclararlo, pero yo lo he hecho en distintas consolas para que se viese más ordenado, pero se puede hacer todo seguido en la misma consola)


## VER REDES DISPONIBLES Y SU INFORMACIÓN

Para esto primero tendremos que poner la tarjeta de red en modo monitor y luego utilizaremos el siguiente comando:

```
> airodump-ng {tarjeta}
```
Con esto estaremos a la escucha de todos los paquetes que se manden, entonces nos saldrá esta interfaz:
```
 CH 10 ][ Elapsed: 24 s ][ 2022-07-04 11:26 

 BSSID              PWR  Beacons    #Data, #/s  CH   MB   ENC CIPHER  AUTH ESSID

 D8:07:B6:20:CE:4B  -51      107        0    0   3  405   WPA2 CCMP   PSK  TP-LINK_CE4B                                                                                                     
 10:50:72:CB:23:E0  -64        0        1    0   1  130   WPA2 CCMP   PSK  MIWIFI_D6BD                                                                                                      
 B0:95:75:EB:3E:E8  -87       55        3    0   1  130   WPA2 CCMP   PSK  Olalde Informatika                                                                                               
 02:95:75:EB:3E:E8  -84       50        3    0   1  130   WPA2 CCMP   PSK  Clientes Olalde                                                                                                  
 78:29:ED:C6:80:EA  -93       38      248    0  11  130   WPA2 CCMP   PSK  MOVISTAR_80E9                                                                                                    
 B8:D5:26:84:D8:02  -80       59        1    0   2  130   WPA2 CCMP   PSK  MIWIFI_D802                                                                                                      
 2A:24:FF:01:72:08  -88        7        0    0  10  130   WPA2 CCMP   PSK  DIRECT-IB-BRAVIA                                                                                                 
 04:02:1F:ED:5A:F6  -93       10        0    0  10  130   WPA2 CCMP   PSK  HUAWEI-B310-5AF6                                                                                                 
 02:1E:42:3E:CB:F4  -93        3        0    0   6  130   WPA2 CCMP   PSK   AAD611                                                                                                          
 00:1E:42:3E:CB:F4  -93        2        0    0   6  130   OPN              WIFI LA UNION                                                                                                    
 E8:1B:69:09:17:11  -77       80        1    0  10  130   WPA2 CCMP   PSK  Sercomm1710                                                                                                      
 78:94:B4:D3:04:25  -93        1        1    0  10  130   WPA2 CCMP   PSK  vodafone0424                                                                                                     
 B8:BE:F4:6C:7C:27  -93       18        0    0  11  360   WPA2 CCMP   PSK  devolo-649                                                                                                       
 E0:0E:E4:DD:96:30  -93        6        0    0   1  130   WPA2 CCMP   PSK  MIWIFI_8C14                                                                                                      
 4C:1B:86:02:24:96  -93       10        0    0   1  130   WPA2 CCMP   PSK  MiFibra-2494                                                                                                     

 BSSID              STATION            PWR   Rate    Lost    Frames  Notes  Probes

 (not associated)   F6:B9:12:76:76:D3  -90    0 - 1      0        1                                                                                                                          
 10:50:72:CB:23:E0  4C:02:20:EE:6D:FC  -68    0 - 1e     9       10         MIWIFI_D6BD                                                                                                      
 B0:95:75:EB:3E:E8  14:C1:4E:9C:5C:3B  -94    0 - 1e     0        1                                                                                                                          
 78:29:ED:C6:80:EA  98:B8:BA:85:90:60  -86    0 - 1      4        5                                                                                                                          
 E8:1B:69:09:17:11  1A:73:BB:D3:29:12  -82    0 - 1e     0        1 
 ```

Claro, aqui hay mucha información, pero hay que saber interpretarla:

-Lo primero: `Arriba estan las redes o mejor dicho los AP disponibles y abajo estan los clientes de dichas redes`

-`SSID`: es el nombre de la red

-`BSSID`: es la MAC del AP (router, movil que este compartiendo datos...)

-`PwR`: es la calidad de la señal (si es -1 es error) y cuanto mas alto sea el numero, sigifica que el usuario esta mas lejos del AP

-`Beacons`: es un paquete que no es de informacion, es como decir "hola, estoy aqui"

-`Data`: paquetes enviados

-`MB`: velociada maxima permitida por el AP

-`Chanel`: es como en una radio, airodump usa channel hopping para saltar de los canales y ver que paquetes se mandan en cada uno

-`ENC`: es el protocolo de seguridad de la red

-`CIPHER`: es el protocolo de encriptacion

-`STATION`: la MAC de los clientes, porque es importante, `abajo el BSSID es el del AP al que estan conectados`

-`Rate`: es tasa de recepcion del cliente

-`Lost`: son la catidad de paquetes perdidos

-`Frames`: nuemero de paquetes enviados por el cliente al AP


Claro, con esto vemos lo de todas las redes, pero nosotros queremos centrarnos en una en concreto, así que vamos a filtrar esa red que nos interesa para solo centrarnos en esa.


#Filtrar redes con airodump

Esto es parecido a lo anterior, pero más especifico, así solo estaremos analizando esa red que nos interesa~o las redes que tengan ciertas caracteristicas que queramos.

```
#filtrar todas las redes que esten en un canal, es decir que solo nos muestre las redes de dicho canal
> airodump-ng -c {canal} {tarjeta de red}

#filtrar una unica red
> airodump-ng -c {canal} --essid {nombr	e de la red} {tarjeta de red}

#filtrar por el BSSID de la red, es decir la MAC del AP
> airodump-ng -c{canal} --bssid {MAC de la red} {tarjeta de red}
```

Si nos centramos en una unica red podremos ver `arriba: la informacion de la red` y `abajo: los clientes, es decir los dispositivos conectados`.


## EXPORTAR EVIDENCIAS

Claro, esto está muy bien, pero de alguna forma tenemos que descargar la información que estamos viendo, es decir, los paquetes que nos llegan, para luego poder utilizarlos para nuestro veneficio, ya sea para algunos ataques, para espiar a los que estén en esa red o bien para conseguir un `Handshake` que hayamos capturado para luego poder desencriptarlo y conseguir la contraseña.

```
# primero vamos a crear un directorio donde vamos a guardar las evidencias
> mkdir {nombre del directorio}
> cd !$ 

# ahora vamos a guardar toda la informacion que iremos capturando a partir de ahora, esto se guardara en el archivo Captura.cap
> airodump-ng -c {canal} -w Captura --bssid {mac del router} {tarjeta} 

# ahora vamos a ver desde otra consola lo que pesa ese archivo, es decir, la cantidad de informacion que hemos capturado
> watch -n {cada cuantos segundos quieres que se ejecute este comando} du -hc 	captura-01.cap
```

Esto es esencial para lo siguiente que vamos a ver, así que es importante que esto lo sepamos hacer bien.
