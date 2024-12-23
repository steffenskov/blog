---
id: 407
title: 'Easiest way to mount network shares on Linux using _netdev'
date: '2021-02-07T15:43:47+01:00'
author: Steffen
layout: post
guid: 'https://www.ckode.dk/?p=407'
permalink: /linux/easiest-way-to-mount-network-shares-on-linux-using-_netdev/
categories:
    - Linux
---

I’ve been switches a bit between distros from the Debian family and the Red Hat family. In doing so I’ve also been switching between using if-up.d and NetworkManager/dispatcher.d for mounting and unmounting my NAS network shares as one wasn’t available on one distro, and vice versa.

The way I had it set up, was I added all the mounting points to fstab, with the “noauto” option, so it wouldn’t automatically attempt to mount them, since this would occur prior to the network being up. I then wrote added mount and unmount scripts to the up/pre-down events.

However the other day, when I was once again contemplating a distro hop, I started looking into doing this in a more uniform way. And lo and behold I learned about the “\_netdev” option for fstab.

The way this works is super simple AND cross distro compatible. Simply add the \_netdev option to your option line in fstab, and the “automount” will wait for network to be up before attempting to mount those points. Furthermore it also automatically unmounts before shutting down the network.

This may be old news to some, but as I merely stumbled upon it and saw dozens upon dozens of questions about this on different forums, I figure it isn’t all that well known.

And for good measure, here’s an example of how such an fstab line would look:

`//NAS/Pictures /home/me/Pictures cifs _netdev,uid=USERNAME,gid=USERNAME,username=USERNAME,password=PASSWORD,iocharset=utf8,vers=3.0 0 0`