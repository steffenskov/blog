---
title: 'Preventing Java updates from installing Ask toolbar'
layout: post
---

If you’re like me, you absolutely hate Java updates, because they try to stuff some sort of crapware down your throat. Most commonly it’s the Ask toolbar, but McAfee antivirus has been around as well.

Luckily there’s a simple way to prevent this problem, you simply need to add 2 registry keys to the Windows registry, and Java won’t even ask you for the toolbars.

Do note that this will only work as long as Java want it to, since their updater is the one reading these keys (in other words, don’t stop looking through the next before clicking the “next” button when updating)

The keys you need to add are these:

`"HKEY_LOCAL_MACHINE\SOFTWARE\JavaSoft\SPONSORS" = "DISABLE"
"HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\JavaSoft\SPONSORS" = "DISABLE"`

Or if you’re a bit uncertain about using the registry editor, simply download this file: [Disable Java crapware.zip](http://www.ckode.dk/wordpress/wp-content/uploads/2014/03/Disable-Java-crapware.zip), unzip it, then run it and answer yes when it asks if you want to import the information.

NOTE: When trying to download this file in Google Chrome (and maybe other browsers, haven’t checked) it’ll warn you about the file being dangerous, this is not because the file itself is dangerous, but simply because it detects the registry file inside, and warns against ALL registry files.

If you’re uncomfortable with this security warning, you can always just enter the keys manually 🙂

I highly recommend doing this on every single Windows machine you ever come across – trust me when I say it’ll give you less work in the long run (I often have to clean out people’s computers, because they’ve installed all sorts of crapware)