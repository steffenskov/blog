---
title: 'Switching from Ubuntu to Fedora - Part 1'
layout: post
---

I’ve been running Ubuntu “server” for quite some time on my workstation now, and lately the upgrade to 17.10 completely broke it. (I did a “do-release-upgrade”, things might have been different with a clean installation)

It wouldn’t mount my NAS, due to smb defaulting to version 3 instead of 1, this was easily fixed by setting vers=1.0 in fstab, however doing so slowed down my boot immensely compared to 17.04.

Furthermore my openvpn tunnel broke, and I never figured out what went wrong with that.

So long story short, I figured I’d try something else, and as Fedora had a “minimal install” option, I immediately fell in love. (This was the same reason I ran the Ubuntu server version – to minimize needless overhead I don’t use)

Having installed Fedora 26 I got a surprise: It booted so fast compared to what my Ubuntu server installation ever did.  
I figure it’s because Ubuntu server isn’t really a minimal install, despite not choosing any package groups during installation. It runs a lot of services out-of-the-box.

Fedora’s minimal on the other hand is truly minimal, so much so that I missed a lot of tools, and had to install those manually whereas I’d been used to just having them on Ubuntu.

Furthermore employing vers=1.0 in fstab didn’t cause any slowdowns to the Fedora installation.

I did however encounter quite a few hickups along the way, and I’ll address each of these in a separate post, to keep them small and easily searchable.

However I’ll cut straight to the conclusion: I’m not going back to Ubuntu after trying Fedora out, it’s just so blazingly fast I still can’t quite fathom it 😀