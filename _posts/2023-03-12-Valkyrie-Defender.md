---
layout: single
title: Valkyrie Defender
excerpt: "This is a guide of my discord servers security bot."
date: 2023-03-12
classes: wide
header:
  teaser: /assets/images/valkyrie_defender/miniatura.png
  teaser_home_page: true
  icon:
categories:
  - Programacion
tags:  
  - Python
---

![](/assets/images/valkyrie_defender/miniatura.png)

## INDEX

- [INTRODUCTION AND URL](#1)
- [ADMINISTRATION CHANNEL AND ROLE](#ADMIN)
- [SERVER CONFIG](#CONFIG)
- [PENALIZATION SISTEM](#SANCTIONS)
- [HELP](#HELP)
- [USER COMMANDS](#USERCOMMANDS)
- [MEMBER INFO](#MINFO)
- [SERVER INFO](#SINFO)
- 

<a id="1"></a>
# INTRODUCTION

![](/assets/images/valkyrie_defender/bot.JPG)

This is a bot to automate and improve discord servers security. The bot has a lot of functions, punishment system, detection spam, malicious links and files, NSFW content, adminstration commands... And with a configuration that can be adapted to each server individually.

Also, the different servers share a blacklist of banned users, which allows you to know if a new user has been banned in another Discord and why. And if you want, you can automate the banning of blacklisted users from the configuration, all through simple commands that automate everything possible.

It reports all incidents to users with the 'valkyrie_admin' role and most of the time in the 'valkyrie' administration channel.

Invite the bot to your Discord server:

[https://discord.com/api/oauth2/authorize?client_id=1080634571734908928&permissions=8&scope=bot](https://discord.com/api/oauth2/authorize?client_id=1080634571734908928&permissions=8&scope=bot)

Bot code:

[https://github.com/AirilMusic/bots/blob/main/Valkyrie%20Defender/main.py](https://github.com/AirilMusic/bots/blob/main/Valkyrie%20Defender/main.py)

Video:



Donations:



<a id="ADMIN"></a>
# ADMINISTRATION CHANNEL AND ROLE

For the administration of this bot, there is an 'valkyrie_admin' administrator role that has some permissions such as the ability to use exclusive commands. The bot notifies these users of important alerts and they can interact with it fully. To do this, you need to manually create this role from the server configuration.

![](/assets/images/valkyrie_defender/role.JPG)

It also has an administration channel that needs to be created for the bot to report incidents through that channel 'valkyrie'.

![](/assets/images/valkyrie_defender/channel.JPG)

<a id="CONFIG"></a>
# SERVER CONFIG

Each server has several options that can be configured to customize some functions of the bot for the needs of that server.

The configuration is saved as follows:

```py
{server_id : {badwords : [list], ban_blacklisted_users : True/False, user_verification : True/False}}
```

I have put few options to simplify the configuration, since most of it is automated, but if necessary in the future, I will add more to adapt the bot to the needs of each server.

Badwords:
This is a list of sanctioned words on the server. Every time a user without the 'valkyrie_admin' role uses one of these words, they will receive a warning (with 3 warnings they will be sanctioned once).

To view the list of badwords (all users can do it):

```
*show_badwords
```

If the list is empty, this message will appear:

But if not, it will display the list:

To add a badword to the list (only users with the 'valkyrie_admin' role can execute this command):

```
*add_badword {word}
```

To remove a badword from the list:

```
*remove_badword {word}
```

Ban blacklisted users:

By default, this option will be False, which will cause blacklisted users in the blacklist shared by all servers that have this bot not to be automatically banned when they first enter the server, but it will notify users with the 'valkyrie_admin' role through the 'valkyrie' administration channel that a blacklisted user has entered.

But if it is activated, if a blacklisted user enters, they will be automatically banned.

Enable this option:

```
*ban_blacklisted_ON
```

Disable this option:

```
*ban_blacklisted_OFF
```

User verification:

This option is disabled by default, so when a new user enters, it does nothing, but it can be activated so that when a new user enters, the bot will send them a message in private that they will have to respond to with the word "verify" within a minute to be able to enter the server, otherwise they will be kicked. This can be useful to prevent bot attacks.

Enable the option:

```
*new_member_verification_ON
```

Disable the option:

```
*new_member_verification_OFF
```

