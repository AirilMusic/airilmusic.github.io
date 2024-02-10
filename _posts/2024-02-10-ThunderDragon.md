---
layout: single
title: Thunder Dragon
excerpt: "This is the user guide of my pentesting tool Thunder dragon."
date: 2024-02-10
classes: wide
header:
  teaser: /assets/images/ThunderDragon/ThunderDragon.webp
  teaser_home_page: true
  icon: 
categories:
  - Wifi-Hacking
  - Web-Hacking
  - Programacion
tags:  
  - Python
---

![](/assets/images/ThunderDragon/ThunderDragon.webp)

## INDEX

- [INTRODUCTION](#introduction)
- [HOW TO USE](#how)
- [ENUMERATION](#enumeration)
- [AUTOMATIC](#auto)
- [ATTACKS](#attacks)
- [WIFI HACKING](#wifi)
- [BLUETHOOD HACKING](#bluethood)
- [OTHER FUNCTIONS](#other)

<a id="introduction"></a>
## INTRODUCTION

My goal with this tool is to simplify and automate the processes of enumeration, attacks, and other cybersecurity tasks, making them accessible and user-friendly for both seasoned professionals and novices alike. Designed to evolve gradually, this project is a long-term endeavor, and I appreciate your patience and understanding for any initial shortcomings or errors.

For enhanced accessibility, the tool supports both Linux and Windows platforms, although certain features might be Linux-exclusive. This dual-platform compatibility ensures the tool's utility in a wide range of educational settings, from teaching basic pentesting and vulnerability assessment in computer science classes to demonstrating the critical importance of cybersecurity and risk management.

All designed with an intuitive user interface that streamlines complex tasks. 

**I do not take responsibility for the misuse that may be made of this tool.**

<a id="how"></a>
## HOW TO USE

Before starting to use this tool, we need to download it and install the requirements. To do this, we can download it manually and install the requirements found in requirements.txt using pip, or more quickly through the console in the following way:

Linux:

```bash
git clone https://github.com/AirilMusic/Pentesting-Hacking/tree/main/ThunderDragon
cd ThunderDragon
pip install -r requirements.txt
```

Windows:

```
git clone https://github.com/AirilMusic/Pentesting-Hacking/tree/main/ThunderDragon
cd ThunderDragon
pip install -r requirements.txt
pip3 install -r requirements.txt
```

And now we can run the tool:

```
python main.py
```

And now we can use the help command to see the options we can use (the image is just a reference, because over time I will be adding more options):

![](/assets/images/ThunderDragon/help.png)
