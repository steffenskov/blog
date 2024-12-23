---
title: 'Getting rid of invisible space used on harddrive'
layout: post
---

One of my harddrives recently started filling up real fast, so I looked into what was taking up all that space.

Funnily enough if I marked all folders/files on the drive, they didn’t take up nearly the space that Windows reported was used on that drive. (Yes I did show hidden files, as well as hidden operating system files)

The drive is mainly used for backup, and Windows backup (Windows 7 pro) reported using a whooping 70gb, of which 65 were just for my System Image. That didn’t sound quite right, as my System partition is only 20gb large.

So I tried wiping the system image backup, since I’m mainly concerned about the data (which are only 5gb). After doing that it still consumed 35gb reported as “System Image”.

Very weird, since Windows backup claims there are no more images left…

Knowing that the Windows backup tool uses the shadow copy provider I checked my System Restore settings, and there it was, using the remaining 35gb. I know for sure it’s all Windows backup files, as I’ve disabled System Restore on that drive.

You’ll find the System Restore window here:

1. Right-click on “Computer” in the start menu and choose properties.
2. Click “System protection” in the left-side menu.

Mark a drive in the list and click “Configure”, and you’ll get a nice slider to set how much space it may use, as well as a “Delete” button which removes everything.