---
title: 'My journey to Linux - Part 3'
layout: post
---

In Part 3 I go over mounting my Synology NAS, which proved trickier than I anticipated, as well as connecting to a Juniper VPN.

Before this however, I had a “one step forwards, two steps backwards” experience I want to share:

As you should know by now, I’m running Linux Mint, or rather I were… The reason being, I experienced very unstable network with my Mint installation. When downloading files it would often stop entirely, only to resume around 10 seconds later.  
I eventually installed Steam (as easy as on Windows btw.) and noticed the same behavior in that.  
Furthermore in its log files I found content\_log.txt which was filled to the brim with HTTP errors of all sorts (403, 404, 503, 500) Certainly something was wrong.  
I then installed Mint in a virtual machine using VirtualBox, and it would work just fine in there.

So I figured I had to reinstall the entire OS – no big deal, as I hadn’t set everything up yet anyway.

However in the meantime I’d grown annoyed at Mint for lacking certain Windows shortcuts, namely Super + X, with X being the numbers 1-9. This shortcut in Windows activates or starts whatever is at that position in the taskbar. And I really missed a vertical taskbar – Mint had an extension at one point, but it’s nowhere to be found in 17.2.  
Doing some research I quickly discovered that Ubuntu Unity does exactly what I wanted – the taskbar is vertical AND it has automatic Super + X shortcuts. So I wound up installing Ubuntu with Unity instead of Mint.

So with that in mind, the next parts about mounting the NAS and Juniper VPN are primarily tested on Ubuntu 15.10. However I will mention any differences when using Mint as well.

<a name="NAS"></a>

## Mounting my Synology NAS

This actually took a lot of effort on both Mint and Ubuntu. Funnily enough the solutions that worked with Mint failed on Ubuntu – I later discovered they were generally prone to error, and finally found a stable way to do it.

The folders I’m currently mounting are:

- Documents
- Music
- Pictures
- Videos
- .mozilla
- .thunderbird

As well as a few “custom” folders I need too.  
I don’t want Desktop to be mounted, as I won’t be able to put symlinks on the Desktop then (no support for symlinks on CIFS I suppose)  
And I want to mount **.mozilla** and **.thunderbird** to keep my settings on the NAS – this way I won’t have to set them up again after a reinstall.

The solutions I tried for mounting, which didn’t work were:

1. Mounting everything directly to my **/home** in **fstab** with auto mount
2. Mounting everything to /mnt in **fstab** with auto mount, and running **mount –bind** commands in a script
3. Mounting everything in **rc.local**
4. Mounting everything in a custom script located in **rc5.d**

1\. Didn’t work properly because it caused every mounted folder to appear on my desktop as well, which I don’t want. Furthermore I later learned that automounting CIFS in **fstab** is rather unstable – more about that in option 3.

2\. To avoid having the mounted folders appear on my desktop, I figured I’d mount them to folders elsewhere, and use **mount –bind** to have them mounted in my /home folder. Whilst this solves the desktop folder issue, I won’t go in detail because it’s just as unstable as the first approach. Sometimes the PC would just boot with nothing mounted.

3\. In a (admittedly naive) attempt to solve the “nothing mounted at boot” issue, I changed **fstab** to include the **noauto** option, thereby not attempting a mount when parsing **fstab**, but only preparing the mount. I then did the mount commands in **rc.local**, and afterwards did my **mount –bind**. This proved slightly less unstable, but unstable none-the-less.

4\. I now moved the entire mounting script from **rc.local** to a new script called **S99bind-mount** located in **/etc/rc5.d**. Furthmore I added $remote\_fs and $network requirements in the script. To understand why I did this, let’s first figure out what went wrong with **rc.local**.

To figure out what went wrong at boot time, I ran:  
`dmesg | grep cifs_mount`  
Which showed up with a lot of error code 101, which means **Error connecting to socket**. This is because the network interface isn’t up before the mounting is attempted.  
The problem with both **fstab** and **rc.local** is they’re run too early to ensure full network connectivity.  
By using **noauto** in **fstab**, and later mounting in the bind-mount script the issue is less likely, as **rc5.d** is the folder for the last runlevel, which isn’t entered until network is available. **rc.local** on the other hand is actually run as the last script for EACH runlevel, meaning it’ll also run way before network can be ensured.

Now this further increased stability, but still didn’t solve it a 100%. What I finally did, which so far has worked flawlessly, was to move the script to **/etc/network/if-up.d**. This makes perfect sense, because I only want the script to run when I have a working network connection.

So to sum it up: If you need stable mounting of **CIFS** (samba protocol) I’d recommend doing it similarly. Here are my **fstab** and script content:

**Fstab:**  
`# fstab
#
//192.168.0.5/music	/mnt/Music	cifs	noauto,uid=steffen,gid=steffen,username=steffen,password=MYPASSWORD,iocharset=utf8	0	0
//192.168.0.5/video	/mnt/Videos	cifs	noauto,uid=steffen,gid=steffen,username=steffen,password=MYPASSWORD,iocharset=utf8	0	0
# and so forth
`

Admittedly it would be prettier to use a credentials file instead of having my username/password in plain text inside **fstab**. But I’m the only user of the PC, and the entire SSD is encrypted, so I took the lazy route. If you’re on a multi user PC always use a credentials file instead as **fstab** can be read by anyone.  
Do also note that it’s supposed to be only 2 lines, my blog is just too narrow for the text, so it wraps 🙁

**Bind script:**  
`#!/bin/sh -e
mount /mnt/Music
mount /mnt/Videos
# and so forth
mount --bind /mnt/Music /home/steffen/Music
mount --bind /mnt/Videos /home/steffen/Videos
# and so forth
`

Doing this I now have no problems with automatically mounting my NAS 🙂

<a name="Juniper"></a>

## Connecting to Juniper VPN

I had quite my concerns with the Juniper VPN, however this proved to be extremely simple.

The later versions of Openconnect has Juniper support built right in. You need version 7.05 or newer (from early 2015).

For Linux Mint, which is based on Ubuntu 14.04, I had to add a ppa from mook, which can be found here: <https://launchpad.net/~mook/+archive/ubuntu/openconnect>.

To add it, just enter this in a term:  
`sudo app-add-repository ppa:mook/openconnect`  
and run a `sudo apt-get update` afterwards.

For Ubuntu 15.10 the above step wasn’t necessary, it had version 7.06 in its repository already.

So just install Openconnect via `sudo apt-get install openconnect` and all you have to do to connect to a Juniper VPN is run `sudo openconnect --juniper URL` where the URL would be the URL to reach the VPN you want.

Openconnect may ask you if you want to accept a certificate (usually the VPNs use self-signed certificates), you can disable this check by adding `--no-cert-check` to the openconnect command.

And finally to speed things up slightly, you can add `--u USERNAME` with your username, to avoid having to enter it.

I’d recommend tossing the entire thing into a bash script for convenience. Mine looks like this:  
`#!/bin/bash
sudo openconnect --juniper --no-cert-check --u steffen URL
`

That was all for this time, next time I’ll look at installing my printer as well as getting my shortcuts similar to Windows.