---
title: 'Cannot copy files from Android device via USB'
layout: post
---

I recently switched to an Android phone, and after about a months worth of taking photos, I wanted to import them to my NAS using USB.

To my big surprise this didn't work, or rather it bugged out rather quickly. I was allowed to copy roughly 1 file (sometimes even that'd fail) before it started timing out the connection.

Since I'm running Linux as my OS, I tried quite a few different MTP tools (MTP is the file transferring protocol used by Android) to no avail.

Cutting to the chase then, I always have my Android phone in "developer mode", as it allows me to tweak the animation speed etc. for a more responsive device. Apparently when having "developer mode" activated, I had to always tick "USB Debugging" in order to have stable file transfers.


WHY on earth that's the case I have no clue, but it immediately solved my issue.

Oh and the phone in question was a Motorola Neo 40 Edge - YMMV depending on device I'd wager.
