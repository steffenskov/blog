---
id: 392
title: 'Warning: locale not supported by Xlib, locale set to C'
date: '2018-07-08T15:58:31+01:00'
author: Steffen
excerpt: "If you're reading this, you've probably received the warning when running for instance <code>xterm</code> or <code>dmenu_run</code>.\r\n\r\nHere's how to easily fix the problem."
layout: post
guid: 'http://www.ckode.dk/?p=392'
permalink: /linux/warning-locale-not-supported-by-xlib-locale-set-to-c/
categories:
    - Linux
---

If you’re reading this, you’ve probably received the warning when running for instance `xterm` or `dmenu_run`.

I suddenly started getting crashes in dmenu whenever I started typing, which led me to run it manually from a terminal. And it wrote “warning: no locale support”

After a bunch of investigating I found out xterm was having issues as well, writing “warning: locale not supported by Xlib, locale set to C” whenever I started a new xterm.

I ran `locale` to check my locale settings, and they all looked fine – every single one except LC\_ALL were set to en\_DK.UTF8 which is the correct behavior. (I’m in Denmark but prefer the English language, hence the en\_DK locale)

A forum post however gave me an idea: Setting `LC_CTYPE=en_US.UTF-8` and sure enough it worked!

So my final fix, was to append this line to `/etc/locale.conf` and reboot (note: requires sudo access):  
`LC_CTYPE=en_US.UTF-8`

Hope this helps you 🙂