---
id: 406
title: 'How to fix MS RDP only being able to connect once per reboot'
date: '2021-02-07T15:26:10+01:00'
author: Steffen
layout: post
guid: 'https://www.ckode.dk/?p=406'
permalink: /desktop-machines/how-to-fix-ms-rdp-only-being-able-to-connect-once-per-reboot/
categories:
    - 'Desktop machines'
---

A somewhat recent Windows Update unfortunately introduced this bizarre bug. The bug affects the machine you’re trying to connect TO, not from. As such this is quite a huge issue if you work from home.

The way the bug behaves is, the host machine only allows a single RDP connection to be made per reboot. So you’d need to end the day with a reboot of your host machine, in order to connect again the day after.

The bug actually stems from the WDDM (Windows Display Driver Model) subsystem, and this is used to render the remote desktop when using RDP. Fortunately a somewhat easy fix exists: Simply disable WDDM for RDP. This forces the use of the older XDDM subsystem, which hails back from Windows 2000. I’ve heard of no issues with this approach, however I’d assume the XDDM subsystem is less efficient, so performance **could** be altered.

If you want to give the fix a try, it’s as easy as running this command in an elevated cmd on the host machine. It simply writes a single value to the registry database. After doing so you’ll need to reboot the host for it to take effect.

`REG ADD “HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services” /v “fEnableWddmDriver” /t REG_DWORD /d 0 /f`

Should you wish to undo the fix, just delete the “fEnableWddmDriver” key from your registry and reboot, simple as that.