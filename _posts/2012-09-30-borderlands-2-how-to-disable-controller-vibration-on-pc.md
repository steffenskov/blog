---
id: 203
title: 'Borderlands 2: How to disable controller vibration on PC'
date: '2012-09-30T18:00:14+01:00'
author: Steffen
excerpt: "I'm playing Borderlands 2 with a wired Xbox 360 controller, but this method should work for any controller.\r\n\r\nIf you've acquired the gun <a href=\"http://www.bl2wiki.com/Miss_Moxxi's_Good_Touch\" target=\"_blank\">\"Moxxi's good touch\"</a> you're probably very annoyed by the constant vibration this causes."
layout: post
guid: 'http://www.ckode.dk/?p=203'
permalink: /gaming/borderlands-2-how-to-disable-controller-vibration-on-pc/
categories:
    - Gaming
tags:
    - 'Borderlands 2'
---

I’m playing Borderlands 2 with a wired Xbox 360 controller, but this method should work for any controller.

If you’ve acquired the gun [**“Moxxi’s good touch”**](http://www.bl2wiki.com/Miss_Moxxi's_Good_Touch) you’re probably very annoyed by the constant vibration this causes.

In the game options interface there’s no way to disable this vibration, but luckily there’s a configuration file which can be altered to handle it.

I’m running Windows 7, so the path to the file may differ if you’re on e.g. Windows XP.

In Windows 7 (and I’ll bet Vista as well), the file is placed in:  
**“C:\\Users\\YOURNAME\\Documents\\My Games\\Borderlands 2\\WillowGame\\Config”**.

The first part is just the physical path to your documents folder, so you’ll have to change YOURNAME, to whatever username you’re running in Windows.

Now open the file **“willowgame.ini”** in notepad (or whatever text editor you prefer) and follow these steps:

1. Search for **“ForceFeedbackManagerClassName=WinDrv.XnaForceFeedbackManager”**
2. Replace this line with **“ForceFeedbackManagerClassName=no.XnaForceFeedbackManager”**
3. Save the file and get back to playing – this time without vibration 🙂