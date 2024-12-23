---
title: 'Issues with clean install of Windows 8'
layout: post
---

If you haven’t noticed, Windows 8 upgrade licenses are currently dirt cheap, going for around 40$ at [www.windows.com](http://www.windows.com).

At that price I couldn’t help but get it.

Installing it gave me 3 issues I believe others may face, so here’s how to fix those.

If you’ve already installed it, and are facing one of these issues, feel free to jump straight to that section:

1. [Activating Windows](#activating)
2. [Changing wallpaper](#wallpaper)
3. [Error when running Windows Update](#update)

And back to the story 🙂

I’ve always preferred clean installs of Windows, to avoid old drivers etc. So I started by removing all partitions from my main drive, this was probably a mistake…

It seems the Windows 8 upgrade installer will detect Windows 7’s presence when installing, but as I had removed the partition completely it couldn’t.

And this in turn lead to my first problem:

### <a name="activating">Activating Windows</a>

When I tried activaing Windows it merely gave me an error about the license (I think it was about the license, not a 100% sure though)

Regardless a bit of searching the web lead to me a simple registry fix.

Open up the registry editor and navigate to  
`[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\OOBE]`  
Here you want to change the value of the DWORD `MediaBootInstall` to `0`.

Afterwards you need to start an administrative command prompt (Hold Windows key and press X, it’ll show a menu with an item called “Command Prompt (Admin)”). In this command prompt run this command: `slmgr /rearm` which will re-enable activation.

Finally reboot the machine and it’ll either activate automatically (mine did) or allow you to do it yourself.

Apparently this value would had been 0 already had I not removed my Windows 7 partition before running the Windows 8 installer.

Having fixed this all was well, until I decided to get rid of the default wallpaper. I could only set solid colors for background, any attempt at using another theme or another image file would simply revert to the default wallpaper.

This leads to the second issue:

### <a name="wallpaper">Changing wallpaper</a>

Once again some searching told me how to fix it, however this time around I found nothing about WHY this is a problem in the first place.

Also once again you’ll need to open your registry editor.  
Navigate to  
`[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\ActiveDesktop]`  
Here you want to change the value of the DWORD `NoChangingWallPaper` to `0`.

Reboot afterwards and you should be able to change the wallpaper.

**IF** this doesn’t work, you’ll have to edit another registry key (the above was however enough for me to fix it)

This time navigate to  
`[HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies]`  
Here you want to first create the key `ActiveDesktop` unless it already exists of course (it usually doesn’t)  
Under this key you then create the DWORD `NoChangingWallPaper` and set it to `0`

Once again reboot for the changes to take effect.

So that’s 2 issues solved, the third came when I wanted to run a Windows Update:

### <a name="update">Error when running Windows Update</a>

Windows Update initially told me about 6 important updates, so obviously I clicked “Install”. However afterwards it wanted a reboot, and during that reboot it wrote something about the updates failing, and it had to revert the update.

I then tried running Windows Update again. This time it found less updates, so I figured some of the previous one must had been installed correctly. I clicked “Install” again, and this time it didn’t require a reboot, but in turn came up with an error.

Turned out the update it couldn’t install was `Microsoft Browser Choice Screen Update for EEA Users of Windows 8 for x64-based Systems (KB976002)`.

The only solution I’ve found is to simply hide the update and ignore it. All it does is prompt you once, to let you choose which browser you want to use. Since I’ve already installed Google Chrome I didn’t really care.

And that pretty much concludes my install of Windows 8.

Overall I had more issues when installing Windows 7, so I’m pleased so far 🙂