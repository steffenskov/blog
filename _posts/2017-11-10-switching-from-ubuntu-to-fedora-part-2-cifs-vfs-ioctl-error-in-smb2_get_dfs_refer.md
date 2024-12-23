---
id: 380
title: 'Switching from Ubuntu to Fedora &#8211; part 2: CIFS VFS: ioctl error in smb2_get_dfs_refer'
date: '2017-11-10T14:37:42+01:00'
author: Steffen
excerpt: "After switching to kernel 4.13 I suddenly got a bunch of these errors when attempting to mount my NAS using CIFS (samba):\r\nCIFS VFS: ioctl error in smb2_get_dfs_refer\r\n\r\nHere's how to easily solve the problem."
layout: post
guid: 'http://www.ckode.dk/?p=380'
permalink: /linux/switching-from-ubuntu-to-fedora-part-2-cifs-vfs-ioctl-error-in-smb2_get_dfs_refer/
categories:
    - Linux
---

Quite the header right?

Still it serves a purpose: I found A LOT of people having this issue when getting onto Linux kernel 4.13, and very few actual answers to how to fix it. This way Google should pick up the post, so people can easily find the solution 🙂

On to the topic: After upgrading my Ubuntu 17.04 to 17.10, and thus getting onto kernel 4.13, I suddenly got a bunch of these errors during boot:

CIFS VFS: ioctl error in smb2\_get\_dfs\_refer

They were caused by attempting to mount my Synology NAS using CIFS.

Switching to Fedora 26 didn’t solve this, as the problem is caused by kernel 4.13.

You see I’d always gone with the default settings in /etc/fstab, but after A LOT of searching, I finally discovered that kernel 4.13 changes the default from smb version 1 to version 3.  
Version 3 has been around for quite a long time, however unless you specifically asked for in fstab, you just got version 1.

I should mention that version 3 brings a lot of security improvements, and if you’re at all able to run version 3, you should!

Back to the problem, my Synology NAS is rather new (415+) and runs the latest DSM with full smb version 3 support. However for reasons beyond me, I still got all these errors. Some forums mentioned it being a bug in the smb driver implementation – if this is the case, fixing it is not easily done by yourself.

Therefore I resolved to use the very simple (some might even call it subpar) fix: Going back to version 1 which has always worked just fine for me.

And doing so is luckily very simple: Just add “vers=1.0” to your options in /etc/fstab and you’re done 🙂

Here’s an excerpt from my fstab as an example:  
`<br></br>//192.168.0.2	/mnt/NAS	cifs	noauto,uid=NAME,gid=NAME,username=NAME,password=PASS,iocharset=utf8,vers=1.0	0	0<br></br>`

As you can see I just appended “vers=1.0” at the end of the options.

Hope this helps you out, and feel free to leave a comment if you have any questions or feedback 🙂