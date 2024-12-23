---
id: 281
title: 'My journey to Linux &#8211; Part 2'
date: '2015-11-01T20:21:08+01:00'
author: Steffen
excerpt: "In part 2 I'll go over the installation process as well as getting hardware accelerated full disk encryption running, using my Samsung EVO 850's SED (Self Encrypting Drive) feature.\r\n\r\nI ran into the common \"grub-efi-amd64-signed failed to install into /target/\" error message, as well as \"Not authorized\" when trying to encrypt my drives. Both of which I finally solved."
layout: post
guid: 'http://www.ckode.dk/?p=281'
permalink: /linux/my-journey-to-linux-part-2/
categories:
    - Linux
tags:
    - Encryption
    - Linux
---

In part 2 I’ll go over the installation process as well as getting hardware accelerated full disk encryption running, using my Samsung 850 EVO’s SED (Self Encrypting Drive) feature.

<a name="Install"></a>

## Installation

I downloaded the latest Linux Mint (17.2 at the moment) and used [Unetbootin](https://unetbootin.github.io/) to create a bootable USB drive.

Then I rebooted the machine with the USB drive and started the installation – everything went well until it was almost done, where it crashed with this error message:  
“grub-efi-amd64-signed failed to install into /target/” (and some more text I don’t remember)

Via google I found out a lot of people had this issue, but few found a proper solution – I read about a lot of hacks and work-arounds, but no real explanation of what caused it.

I figured it had to do with UEFI mode, since it’s an EFI error. So my first attempt at solving it was to put my UEFI (also known as BIOS, but its proper name now a days is UEFI) into legacy mode.

To do this I restarted and entered the UEFI, here I enabled the CSM feature (Compatibility Support Module) and disabled Secure Boot.  
On my motherboard (ASUS Z77 based) I have to delete the Secure Boot keys – don’t worry if you have to too, they can always be reinstalled directly in the UEFI, should you want to return to Windows.

Sure enough Linux Mint would install now, however I wasn’t satisfied as I knew for a fact UEFI mode should be supported, and it just didn’t feel right to have to emulate an old BIOS.

So I tried again, this time I disabled the CSM again, to put everything into UEFI mode, but I kept Secure Boot disabled.

And that did the trick, now Linux Mint installed just fine in UEFI mode.

So to recap: If you get the dreaded “grub-efi-amd64-signed failed to install into /target/” error message on a fresh install, try disabling Secure Boot in your UEFI and retry the install, it’ll probably work.

<a name="Encryption"></a>

## Encryption

I have 2 SSDs in my PC: One Samsung 850 PRO and one Samsung 850 EVO. Both of them support the OPAL standard for encryption, and both of them had run just fine in Windows eDrive mode (see my blog post about eDrive here: [How to enable Windows eDrive encryption for SSDs/](http://www.ckode.dk/desktop-machines/how-to-enable-windows-edrive-encryption-for-ssds/), if you’re interested)

I started out by downloading MSED from www.r0m30.com/ where I download both the Linux tool and the PBA image. However there is a newer version elsewhere, so don’t go to r0m30’s just yet!

The new tool is a merge of MSED and Drive-Trust-Alliance, and can be found here: [github.com/Drive-Trust-Alliance/sedutil/wiki/Encrypting-your-drive](https://github.com/Drive-Trust-Alliance/sedutil/wiki/Encrypting-your-drive).  
You’ll want to download the [Linux tool](https://github.com/Drive-Trust-Alliance/exec/blob/master/sedutil_LINUX.tgz?raw=true) and either the [BIOS image](https://github.com/Drive-Trust-Alliance/exec/blob/master/LINUXPBARelease.img.gz?raw=true) or the [UEFI image](https://github.com/Drive-Trust-Alliance/exec/blob/master/UEFI64_Release.img.gz?raw=true) depending on which mode you installed Linux.  
I’d recommend installing in UEFI mode as it’s newer and BIOS is a legacy feature which may not be supported by everything in the future.

After downloading the tool and image I followed the instructions to the letter, but kept getting “NOT AUTHORIZED” when running the first command “initialsetup”. Eventually I found out it’s because of Windows eDrive. Windows had initialized my drives, and as such MSED couldn’t.

The cure for this problem is to reset the drives using their PSID. The PSID is a 32 cipher long key printed on the label of the actual drive.  
So I had to get both drives out of my PC, take a picture of their labels, reinsert them and reset them.  
Resetting them also erases them, so I had to do another install afterwards.

Here are the exact steps necessary to reset a drive:

1. Install Linux
2. Download the [Linux tool](https://github.com/Drive-Trust-Alliance/exec/blob/master/sedutil_LINUX.tgz?raw=true) from Drive-Trust-Alliance
3. Enable libata.allow\_tpm (See point 4,5 and 6 [here](#libata))
4. Reboot
5. run the command “sudo ./sedutil-cli –yesIreallywanttoERASEALLmydatausingthePSID {PSID} {DRIVE}”

In the above {PSID} would be the 32 ciphers, and {DRIVE} would for instance be /dev/sda.

Here’s a picture of the PSID on my Samsung 850 EVO: [![](http://www.ckode.dk/wordpress/wp-content/uploads/2015/10/Samsung-EVO-850-PSID_thumb.jpg)](http://www.ckode.dk/wordpress/wp-content/uploads/2015/10/Samsung-EVO-850-PSID.jpg)

This will erase the drive, even if it doesn’t seem to have done so. Reboot the PC and reinstall Linux at this point.

Having finally reset both my drives, I reinstalled and finally got encryption running.

I have my boot drive as /dev/sda, and my secondary non-bootable drive as /dev/sdb.

Here are the exact steps to encrypt both my drives:

1. <a name="libata"></a>Install Linux
2. Download and extract the [Linux tool](https://github.com/Drive-Trust-Alliance/exec/blob/master/sedutil_LINUX.tgz?raw=true) from Drive-Trust-Alliance (you likely want the x86-64 version)
3. Download and extract the [UEFI image](https://github.com/Drive-Trust-Alliance/exec/blob/master/UEFI64_Release.img.gz?raw=true) from Drive-Trust-Alliance
4. Edit /etc/default/grub to enable libata.allow\_tpm. You need to find the line that starts with “GRUB\_CMDLINE\_LINUX\_DEFAULT” and add “libata.allow\_tpm=1″ inside the quotes on the line. My final line reads like this:  
    GRUB\_CMDLINE\_LINUX\_DEFAULT=”quiet splash libata.allow\_tpm=1”
5. run “sudo update-grub”
6. Reboot
7. Open a terminal and navigate to the folder where you extracted tool and image.
8. run “sudo ./sedutil-cli –initialsetup {CHOSEN\_PASSWORD} /dev/sda”
9. run “sudo ./sedutil-cli –-loadPBAimage {CHOSEN\_PASSWORD} {UEFI\_IMG\_FILENAME} /dev/sda”
10. run “sudo ./sedutil-cli –-setMBREnable on {CHOSEN\_PASSWORD} /dev/sda”
11. run “sudo ./sedutil-cli –-enableLockingRange 0 {CHOSEN\_PASSWORD} /dev/sda”
12. I did a reboot here, but it’s likely not necessary. Your boot drive is now encrypted.
13. run “sudo ./sedutil-cli –initialsetup {CHOSEN\_PASSWORD} /dev/sdb”
14. run “sudo ./sedutil-cli –enableLockingRange 0 {CHOSEN\_PASSWORD} /dev/sdb”
15. The secondary drive is now encrypted. Note fewer steps are required because it’s not bootable.
16. Once you reboot you should be met by a simple console asking for the {CHOSEN\_PASSWORD}.

The {UEFI\_IMG\_FILENAME} is the name of the image you downloaded in step 3.  
{CHOSEN\_PASSWORD} can be anything you like, though I’d avoid spaces and special characters if I were you, as different keyboard layouts could pose problems.

If you ever run into problems during the encryption process it should never get worse than you can do a “yesIreallywanttoERASEALLmydatausingthePSID” and retry it, so do not panic if something goes wrong.

I hope this has been of help, next time I’ll go over mounting my NAS to my home subfolders and maybe take a look at some Juniper VPN.