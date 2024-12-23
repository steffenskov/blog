---
id: 235
title: 'League of Legends: Constantly getting disconnected from PvP.net?'
date: '2013-12-22T12:05:25+01:00'
author: Steffen
excerpt: "After having reinstalled my PC, I was suddenly getting \"disconnected from PvP.net\" all the time in League of Legends.\r\nIt caused the game to be very unstable, and most of the time I'd just end up being logged out.\r\n\r\nLuckily the solution is quite simple: Disable IPv6."
layout: post
guid: 'http://www.ckode.dk/?p=235'
permalink: /gaming/league-of-legends-constantly-getting-disconnected-from-pvp-net/
categories:
    - Gaming
tags:
    - 'League of Legends'
---

After having reinstalled my PC, I was suddenly getting “disconnected from PvP.net” all the time in League of Legends.  
It caused the game to be very unstable, and most of the time I’d just end up being logged out.

Luckily the solution is quite simple: Disable IPv6.

At the moment IPv6 is hardly used anywhere, so it’s perfectly safe to disable (and furthermore the likelihood of you needing it in your private LAN is even smaller)

Here is how to disable it:

1. Open **Controlpanel**
2. Find **Network and Sharing Center**
3. Click **Change adapter settings**
4. Right-click your network adapter and click **Properties**
5. Find the element called **Internet Protocol Version 6 (TCP/IPv6)** and uncheck it.

When you’re done it should look like this:  
[![Disable ipv6](http://www.ckode.dk/wordpress/wp-content/uploads/2013/12/Disable-ipv6-237x300.png)](http://www.ckode.dk/wordpress/wp-content/uploads/2013/12/Disable-ipv6.png)

UPDATE: You may also have to disable the “IP Helper” service, which is in charge of creating tunnels between IPv6 and IPv4 networks. Again like IPv6 itself this is hardly used anywhere, and is perfectly safe to disable.

Here’s how to disable this one:

1. Press and hold the Windows key, then press R – this should open the “Run” dialog
2. Enter “services.msc” and click the “OK” button
3. You’re now looking at a list of all the services installed on your computer. Scroll down to “IP Helper”
4. Right-click on “IP Helper” and left-click on “properties”
5. Click the “Stop” button in this window
6. Left-click in the “Startup” drop down which is set to “Automatic” by default. Here choose “Disabled”
7. Click the “OK” button
8. Close the list of services on the red cross in the upper right corner, and you’re done

Restart the League of Legends client and the problem should be gone 🙂