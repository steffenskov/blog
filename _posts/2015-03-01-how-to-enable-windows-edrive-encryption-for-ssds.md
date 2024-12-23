---
id: 259
title: 'How to enable Windows eDrive encryption for SSDs'
date: '2015-03-01T17:56:23+01:00'
author: Steffen
excerpt: "As you probably know a lot of SSDs are sold as SED: \"Self Encrypting Device\", what this means is that the SSD can handle encryption itself, so your CPU doesn't have to spend cycles doing it. This in turn actually means NO LOSS of performance, because the SSD is already encrypting all its content, it just doesn't have a password set yet.\r\n\r\n \r\n\r\nHowever enabling it on a desktop PC hasn't always been easy, in fact it's just recently with Windows 8 and the eDrive function, that it's become somewhat simple to do."
layout: post
guid: 'http://www.ckode.dk/?p=259'
permalink: /desktop-machines/how-to-enable-windows-edrive-encryption-for-ssds/
categories:
    - 'Desktop machines'
tags:
    - Encryption
---

As you probably know a lot of SSDs are sold as [SED: “Self Encrypting Device”](http://en.wikipedia.org/wiki/Hardware-based_full_disk_encryption), what this means is that the SSD can handle encryption itself, so your CPU doesn’t have to spend cycles doing it. This in turn actually means NO LOSS of performance, because the SSD is already encrypting all its content, it just doesn’t have a password set yet.

However enabling it on a desktop PC hasn’t always been easy, in fact it’s just recently with Windows 8 and the eDrive function, that it’s become somewhat simple to do.

Most SED drives require you to set a “Hard disk password”, also sometimes known as “ATA password”. This has to be done in UEFI/BIOS and the option normally only exists on laptops, with the exception of a select few desktop motherboards (and I mean very few…)

Now what Windows eDrive does is it let Windows [Bitlocker](http://en.wikipedia.org/wiki/BitLocker) set the password and handle unlocking the drive at boot time, with the actual encryption still being done by the drive itself.

This is opposed to “normal” Bitlocker, where the CPU does the actual encryption (thus causing a loss of performance)

To enable Windows eDrive you need five things:

1. You need Windows 8 Pro (or 10 Pro) – otherwise Bitlocker isn’t available.
2. Windows has to run in UEFI mode, for instructions on how to do this, read this article: [Making Windows run in UEFI mode with Secure Boot](http://www.ckode.dk/desktop-machines/making-windows-run-in-uefi-mode-with-secure-boot/), but don’t reinstall just yet if you have to – complete this article before, because otherwise you’ll have to reinstall twice.
3. You need an eDrive compliant SSD, currently the only SSDs I know which support this are Samsung EVO 840, EVO 850, PRO 850 and Crucial M500 – I own the Samsung EVO 840 and PRO 850 myself, so the rest of the guide will focus on these.
4. You need to configure your SATA ports for AHCI, this means hardware RAID or RAID using the motherboard is not an option. (Note: Don’t change this setting on a running Windows installation, as you’ll likely not be able to boot Windows afterwards – instead wait until you need to do the Secure Erase part (I’ll mention when to do this))
5. If you want to boot from the encrypted SSD you also need a motherboard which supports UEFI version 2.3.1 and has the EFI\_STORAGE\_SECURITY\_COMMAND\_PROTOCOL defined. This part is actually very difficult to ensure, as most motherboard manufacturers don’t specify these things on their websites. From experience I can tell the Gigabyte Z77M D3H doesn’t support this, and that ASUS P8Z77-V LX does. (I have had both boards, had to purchase the ASUS since the Gigabyte didn’t support it)

To make matters worse you won’t know if your system lives up to these three requirements without actually trying.

If you’re interested, you can read Microsoft’s article about these requirements here: <https://technet.microsoft.com/en-us/library/hh831627.aspx>

**Warning: You will need to completely erase your SSD, there’s no way around it! As such enabling encryption on your Windows drive will also require a reinstall of Windows.**

**Furthermore do not ever install Intel Rapid Storage Technology drivers, as they will make it impossible for you to enable hardware accelerated encryption through Bitlocker. It may appear as though it enables, but in fact it’s only the boot password prompt that enables, the encryption does not!**

Now the steps to enable eDrive on Windows for a Samsung EVO 840, EVO 850 or PRO 850 are as follows:

1. Download and install the [Samsung Magician](http://www.samsung.com/samsungssd/) software, which can be found here: <http://www.samsung.com/samsungssd/>
2. After it’s installed, run it and click “Data Security” at the bottom of the left-hand menu.
3. Here you’ll have to select the drive in question in a dropdown at the top and afterwards click the “Enable” button under “Encrypted Drive”
4. Repeat step 3 for all drives you need to encrypt.
5. Now you’ll need to do a “Secure Erase” on all the SSD drives you want to encrypt, click “Secure Erase” in the left-hand menu.
6. **Note: this completely erases the SSD – make sure you have backup of everything you need.**
7. You’ll likely be asked to create a bootable CD or USB stick here, so follow the instructions to do so.
8. Then reboot the PC and enter UEFI (commonly known as BIOS) and ensure the SATA ports are all configured as AHCI. (RAID is thus not an option with hardware encryption)
9. After checking this, boot the PC using the CD or USB stick you just created.
10. If once booted the text looks like gibberish, press the ESCAPE key on your keyboard and enter “segui0 /2” and press enter – the “0” is a zero, not the letter O. this should make the text read fine. If it however doesn’t, press the ESCAPE key again, and try with “segui0 /s” – thanks to Tom Jeanne for this tip.
11. Follow the instructions in the tool to do a secure erase of each drive – the tool will probably say the drive is frozen and you have to unplug the power to the drive and reconnect it. If it says this, you’ll have to unplug the power (NOT THE SATA CABLE) from your SSD with the PC still running, and reconnecting it again after 5 seconds. If you’re on a laptop, it’s okay to remove both the SATA cable and the power cable at once, if they’re in a single connector. (Thanks to Tim Armitage for this tip)
    
    If you’re on a laptop and the tool refuses to do a secure erase (for instance by rebooting automatically when plugging the drive back in), you can check if the manufacturer of your laptop has a secure erase tool themselves. This was for the case for commentator Patrick Plank who owns a Lenovo laptop. (Thanks to Patrick Plank for this tip)
12. Now the tool should complete the secure erase and if you have more SSDs it’ll now ask about the next one, repeat until all the SSDs you want encrypted have been erased. Do notice the secure erase is done very quickly, do not be alarmed by this – it’s perfectly normal.
13. Reboot the PC and install Windows 8 as normal.
14. To ensure you’ve installed in UEFI mode, run msinfo32 and look for “BIOS Mode” in the right-hand part of the window. If it says UEFI you’re ready to continue, and if it says legacy, you need to check your UEFI settings as somethings is forcing it into BIOS emulation mode. The most likely cause is that CSM is still enabled in UEFI. If this happens refer to my article on enabling UEFI mode here: [Making Windows run in UEFI mode with Secure Boot](http://www.ckode.dk/desktop-machines/making-windows-run-in-uefi-mode-with-secure-boot/)
15. Once Windows is properly installed you’ll most likely need to change some settings to allow Bitlocker to work with a password instead of a TPM module. You can skip these steps if you do have a TPM module, but normally you don’t.
16. To enable Bitlocker to use a password run gpedit.msc
17. Navigate to “Computer Configuration” -&gt; “Administrative Templates” -&gt; “Windows Components” -&gt; “Bitlocker Drive Encryption” -&gt; “Operating System Drives”
18. Double-click “Require additional authentication at startup”. NOTE: There are 2 very similar settings, the other one being named “Require additional authentication at startup (Windows Server 2008 and Windows Vista)”. You want the one with the shorter name.
19. Set this to “Enabled” and click “OK” – reboot if Windows asks you to.
20. Now Bitlocker can use a password which you’ll have to enter before Windows boots.
21. Navigate to “Computer” and right-click your C drive (or whichever drive you want to encrypt) and click “Turn on Bitlocker”
22. Bitlocker will ask how you want to unlock the drive – click “Enter a password”
23. Enter whatever password you’d like to use, make sure you can remember it ^^
24. Next Bitlocker asks how to store the recovery key. Personally I prefer to store it on my Microsoft account, but feel free to pick what you’re most comfortable with. Make sure you keep the recovery key safe though, if either you forget the password or Bitlocker somehow screws up you will need the recovery key.
25. Finally Bitlocker will ask to run a system check to ensure everything is in order, make sure the checkmark is set and click “Continue”.
26. **Note: If you get an error message around this time saying “Parameter is incorrect”. OR if you cannot get the PC to boot past the Bitlocker screen, make sure you’ve done a complete Windows Update before enabling Bitlocker. Thanks to commentator Alex for this tip.**
27. After Windows is rebooted Bitlocker may open automatically and tell you the result of the system check. Assuming everything went well it should now ask if you’re ready to encrypt. NOTE: Sometimes it just enables encryption immediately and doesn’t open Bitlocker at all after reboot. You can tell whether your drive is encrypted, by opening “Computer” and looking for the small lock icon on your drive. Or you can run “manage-bde -status C:” from an elevated command prompt. (Thanks to Tim Armitage for pointing this out)
28. **If Bitlocker asks how much of the drive you want to encrypt, STOP!** In this case it is using software encryption, not hardware encryption. This is not what you want. In case this happens, make sure that: 
    1. You’re running Windows in UEFI mode – run msinfo32 and check “BIOS mode” as described in step 13.
    2. You’ve run the secure erase on your SSD AFTER enabling Encryption using Samsung Magician
    3. If you are in UEFI mode and did the secure erase everything points to your motherboard being the culprit – most likely it doesn’t properly support UEFI 2.3.1 or the EFI\_STORAGE\_SECURITY\_COMMAND\_PROTOCOL. In this case you can either go with software encryption which will cause a performance overhead, or purchase a new motherboard 🙁

Phew that should be all there is to it – I’ve no clue why it has to be this difficult, but hopefully it’ll be simpler in the future when all motherboards support UEFI 2.3.1 and run in UEFI mode out-of-the-box.

Hope the guide helps you, if you have any questions or if I missed something feel free to comment 🙂