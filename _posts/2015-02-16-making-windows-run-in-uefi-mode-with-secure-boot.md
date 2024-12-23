---
title: 'Making Windows run in UEFI mode with Secure Boot'
layout: post
---

Here I’ll go over how you enable [UEFI](http://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) mode (and with it [Secure Boot](http://en.wikipedia.org/wiki/Hardware_restriction#Secure_boot)), as well as why you want to do this.

**Warning: You will need to reinstall Windows for this to work, there’s no way around it!**

First let me explain what UEFI is – it’s the successor for BIOS. Yes the setup menu for setting CPU speed, RAM and so forth.

UEFI replaced BIOS some years ago, and with it came the feature Secure Boot.

Secure Boot is a way of booting, where malware cannot hijack the boot process – so it’s means to be a protection from malware such as rootkits.

If this wasn’t enough reason to enable UEFI mode, there’s also multiple motherboards supporting different versions of “fast boot”, which requires UEFI mode to work. These obviously speed up the boot process, and some have a rather high impact on it.

Finally it’s also necessary to utilize Windows E-Drive technology for hardware based encryption of some SSDs.

But wait, I said UEFI replaced BIOS, so how can you NOT run in UEFI mode ? Well this one is rather simple – UEFI supports simulating BIOS, and will do this out of the box on most motherboards.

Now if you have a prebuilt PC, chances are it’s already running UEFI mode. But if you, like me, prefer to build your PC yourself, you’re probably currently stuck in BIOS simulation mode.

On to how to enable it, it’s actually a really simple procedure, albeit somewhat cumbersome.

First off you need the proper hardware:

- Your motherboard needs to run UEFI instead of BIOS
- Your graphics card needs to have an UEFI driver in its firmware. I actually had this problem with an old Geforce GTX 260, which didn’t support UEFI – so when I enabled UEFI mode on the motherboard, the PC wouldn’t boot and I just had a black screen.

With these two things checked, you need to enter the UEFI setup and find the setting for your SATA drives – this should be set to either AHCI or RAID (if you’re using RAID). If it’s set to IDE you cannot use UEFI mode.

Afterwards find the setting called [*CSM*](http://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface#CSM_booting) or *Compatibility Support Module*.

This is the setting that simulates BIOS, so you want to **disable** *CSM*.

Furthermore quite a few UEFIs have a setting for choosing your operating system – if your UEFI has this setting, you want to pick Windows 8, 8.1 or similar.

Finally some UEFIs don’t have any keys installed by default for the Secure Boot part, and I know for a fact that Gigabyte z77-ds3h doesn’t. If your UEFI has a setting which offers to install default keys, you’ll probably need to do that as well. Unfortunately I cannot check the exact wording of the setting, as I’ve since sold the motherboard in question.

Now the final cave eat – you have to reinstall Windows at this point, as Windows will either outright refuse booting at this point, or it’ll be stuck in BIOS mode.

Luckily there’s a simple way to find out if Windows runs in UEFI mode:

- Press WINDOWS key + R to open the run command
- Enter *msinfo32* and press the OK button.
- In this window look for the part called *BIOS mode* in the right side of the window. If it says UEFI, you’re in UEFI mode, and if it says Legacy you’re in BIOS mode.

And there you have it, pretty simple but somewhat cumbersome due to the reinstalling part.