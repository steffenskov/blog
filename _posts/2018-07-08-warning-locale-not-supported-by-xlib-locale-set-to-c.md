---
title: 'Warning: locale not supported by Xlib, locale set to C'
layout: post
---

If you’re reading this, you’ve probably received the warning when running for instance `xterm` or `dmenu_run`.

I suddenly started getting crashes in dmenu whenever I started typing, which led me to run it manually from a terminal. And it wrote “warning: no locale support”

After a bunch of investigating I found out xterm was having issues as well, writing “warning: locale not supported by Xlib, locale set to C” whenever I started a new xterm.

I ran `locale` to check my locale settings, and they all looked fine – every single one except LC\_ALL were set to en\_DK.UTF8 which is the correct behavior. (I’m in Denmark but prefer the English language, hence the en\_DK locale)

A forum post however gave me an idea: Setting `LC_CTYPE=en_US.UTF-8` and sure enough it worked!

So my final fix, was to append this line to `/etc/locale.conf` and reboot (note: requires sudo access):  
`LC_CTYPE=en_US.UTF-8`

Hope this helps you 🙂