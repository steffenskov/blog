---
title: 'Gtk-WARNING: cannot open display 0'
layout: post
---

If you’re seeing this error, you’re probably trying to start a GTK application as root and having no luck doing so.

I first noticed it when trying to run gparted from a terminal using sudo.

The problem is the root user not having access to the X server. And the fix is really simple, just run this command:

`xhost +local:`

Which will grant all local connections access to the X server until the next reboot.

Hope this helps you 🙂