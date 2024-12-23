---
id: 393
title: 'Gtk-WARNING: cannot open display 0'
date: '2018-07-08T16:12:16+01:00'
author: Steffen
excerpt: "If you're seeing this error, you're probably trying to start a GTK application as root and having no luck doing so.\r\n\r\nLuckily the fix is really simple."
layout: post
guid: 'http://www.ckode.dk/?p=393'
permalink: /linux/gtk-warning-cannot-open-display-0/
categories:
    - Linux
---

If you’re seeing this error, you’re probably trying to start a GTK application as root and having no luck doing so.

I first noticed it when trying to run gparted from a terminal using sudo.

The problem is the root user not having access to the X server. And the fix is really simple, just run this command:

`xhost +local:`

Which will grant all local connections access to the X server until the next reboot.

Hope this helps you 🙂