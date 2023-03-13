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
- [PENALTY SYSTEM and BLACKLIST](#SANCTIONS)
- [HELP](#HELP)
- [USER COMMANDS](#USERCOMMANDS)
- [ADMIN COMMANDS](#ADMINCOMMANDS)
- [SPAM LOCKER](#SPAM)
- [SENSIBLE INFO AND PHISHING CHECKER](#INFO)
- [NSFW content detection (with AI)](#NSFW)
- [URL CHECKER](#URL)
- [FILE CHECKER](#FILES)

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

## Badwords:
This is a list of sanctioned words on the server. Every time a user without the 'valkyrie_admin' role uses one of these words, they will receive a warning (with 3 warnings they will be sanctioned once).

![](/assets/images/valkyrie_defender/bw-3.JPG)

To view the list of badwords (all users can do it):

```
*show_badwords
```

If the list is empty, this message will appear:

![](/assets/images/valkyrie_defender/bw-1.JPG)

But if not, it will display the list:

![](/assets/images/valkyrie_defender/bw-5.JPG)

To add a badword to the list (only users with the 'valkyrie_admin' role can execute this command):

```
*add_badword {word}
```

To remove a badword from the list:

```
*remove_badword {word}
```

## Ban blacklisted users:

By default, this option will be False, which will cause blacklisted users in the blacklist shared by all servers that have this bot not to be automatically banned when they first enter the server, but it will notify users with the 'valkyrie_admin' role through the 'valkyrie' administration channel that a blacklisted user has entered.

![](/assets/images/valkyrie_defender/blacklisted_warn.JPG)

But if it is activated, if a blacklisted user enters, they will be automatically banned.

Enable this option:

```
*ban_blacklisted_ON
```

Disable this option:

```
*ban_blacklisted_OFF
```

## User verification:

This option is disabled by default, so when a new user enters, it does nothing, but it can be activated so that when a new user enters, the bot will send them a message in private that they will have to respond to with the word "verify" within a minute to be able to enter the server, otherwise they will be kicked. This can be useful to prevent bot attacks.

![](/assets/images/valkyrie_defender/user_verification.JPG)

Enable the option:

```
*new_member_verification_ON
```

Disable the option:

```
*new_member_verification_OFF
```

<a id="SANCTIONS"></a>
# PENALTY SYSTEM and BLACKLIST

## PENALTY SYSTEM

This bot has a penalty system. The first thing is that there is a warning system, which is like a notice that is not too important, but every 3 accumulated warnings result in a penalty. Each penalty has a different punishment, and the more you have, the stronger the punishment is. If you have 5 penalties, you will be banned.

```
3 Warns --> 1 Penalty

1 Penalty --> An hour of timeout
1 Penalty --> A day of timeout
1 Penalty --> Two days of timeout
1 Penalty --> A week of timeout
1 Penalty --> BAN, and this user will be added to a multiserver blacklist
```

Every time someone is penalized, users with the 'valkyrie_admin' role will be notified through the 'valkyrie' channel, mentioning the user, the sanction, and the reason. When someone is banned, those members will be mentioned to pay more attention to that message to avoid mistakes...

The information about the penalties is saved like this:

```py
{server_id:{user_id:[reasons_list]}}
```

And the information about the warnings is something similar.

To see the sanctions that a user has (any user can do it):

```
*check_sanctions 
```

![](/assets/images/valkyrie_defender/check_sanctions.JPG)

Users with the 'valkyrie_admin' role can also execute some commands to apply and remove these sanctions (and many other things such as spam detection, banned words, etc. that apply these sanctions automatically):

Warn:

```
*warn @user {reason}
```

Apply a penalty:

```
*penalize @user {reason}
```

Clear penaltys:

```
*clear_sanctions
```

Ban a user:

```
*ban @user {reason}
```

Unban a user:

```
*unban @user
```

See banned members list:

```
*banned_members
```

## BLACKLIST

When a user is banned on a server, that user is added to a blacklist that is shared among all servers where the bot is present. Every time a new user enters a server, the bot checks if that user is in the blacklist, and if so, it warns users with the 'valkyrie_admin' role through the 'valkyrie' channel. If the server configuration option is set to ban users who are on the blacklist, new users on the blacklist are banned.

The information about the blacklist is saved like this:

```py
{user_id:[reasons_list]}
```

<a id="SANCTIONS"></a>
# HELP

The bot has a command to help users know which commands they can use and what they do:

```
*Help
```

![](/assets/images/valkyrie_defender/help.JPG)

<a id="USERCOMMANDS"></a>
# USER COMMANDS

These are the commands that all users can execute, regardless of their role.

SERVER INFO: displays server information

```
*server_info
```

![](/assets/images/valkyrie_defender/server_info.JPG)

MEMBER INFO: displays information about a user

```
*member_info @user
```

![](/assets/images/valkyrie_defender/member_info.JPG)

CHECK SANCTIONS: to see how many penalties a user has

```
*check_sanctions @user
```

![](/assets/images/valkyrie_defender/check_sanctions.JPG)

SHOW BADWORDS: to see server sancioned words list

``` 
*show_badwords
```

![](/assets/images/valkyrie_defender/bw-5.JPG)

PING: to check if the bot is working 

```
*ping
```

![](/assets/images/valkyrie_defender/ping.JPG)

HELP: to see all commands and their fuctions

```
*Help
```

<a id="ADMINCOMMANDS"></a>
# ADMIN COMMANDS

## Server management commands:

Delete las x messages:

```
*delete {num}
```

Disconnecta a user from a voice call:

```
*disconnect @user
```

Mute a user in a voice call:

```
*mute @user
```

Unmute a user in a voice call:

```
*unmute @user
```

Deafen a user in a voice call:

```
*deafen @user
```

Undeafen a user in a voice call:

```
*undeafen @user
```

## Penalty system:

To warn a user:

```
*warn @user {reason}
```

Apply a penalty to a user:

```
*penalize @user {reason}
```

Clear a user's penalties:

```
*clear_sanctions @user
```

Ban a user:

```
*ban @user
```

Show banned users:

```
*banned_members
```

Unban a user:

```
*unban @user
```

## Server config:

Forbidden words (badwords):

Add a badword:

```
*add_badword {word}
```

Remove a badword:

```
*remove_badword {word}
```

Ban new blacklisted users:

Enable:

```
*ban_blacklisted_ON
```

Disable:

```
*ban_blacklisted_OFF
```

New member verification:

Enable:

```
*new_member_verification_ON
```

Disable:

```
*new_member_verification_OFF
```

<a id="SPAM"></a>
# SPAM LOCKER

This bot has a spam detection and blocking system based on counting how many times a user has repeated the same message in a row. If the user repeats the same message 4 times in a row, they are warned to stop spamming. If they repeat the same message again, i.e. they send it 5 times, a penalty is applied. This is done to avoid confusion, but also to encourage users or bots who engage in spamming to send as few messages as possible.



