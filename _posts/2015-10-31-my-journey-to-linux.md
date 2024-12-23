---
title: 'My journey to Linux - Part 1'
layout: post
---

Having used Windows since version 3.0 back in the DOS days, I’ve recently decided it’s time to move my workstation to Linux.

This will be the first post in a series, where I go over how the journey starts, what problems arise and (hopefully) how I solve them.

For this first post I’ll go over why, and what considerations I’ve taken before I even begin installing Linux.

So let’s begin 🙂

## Why I want to move to Linux

This all began with Windows 10. Initially I was really excited about a free Windows upgrade (I ran 8.1 beforehand and was actually quite satisfied with it, despite its “Modern UI”).  
However when news started spreading about its invasion of your privacy, my excitement plummeted to ground.  
I care very much about privacy, not because I have some horrible secret to hide, but because I want to be in charge of what information about me is spread – it’s a principle.

So I figured I’d just stay on Windows 8.1 and all was well. Shortly after Microsoft forced an update down our throats, which enabled all the privacy invasive “features” from Windows 10 in Windows 8 and 7 as well, AND with no way to disable it or opt-out.  
Staying on Windows 8.1 was no longer an option, so I upgraded to Windows 10, where at least you can disable parts of these so-called “features”.

After upgrading and doing a clean install, I quickly started having problems with Windows 10 – it’s somewhat sluggish at times, it’s far more unstable than my Windows 8.1 installation was (mainly programs or games crashing) and recently it started booting to a black screen with a mouse cursor, where it’d be stuck for anything from 30 seconds to 5 minutes to forever (I didn’t wait forever, but I did wait more than 10 minutes with nothing happening).  
Furthermore I like to store all my documents, pictures and so forth on a NAS, which means I want my documents folder to map to a network share – Windows (both 8 and 10) doesn’t handle this very well, as it often refuses to navigate to subfolders within these mapped folders.

So to recap: I have a lot of small issues with Windows 10, and now I’ve just had it with it – it’s time for something new.

## What I use my workstation for

Having decided to move away from Windows, the next question I asked myself was: What do I actually use my workstation for, and will it work on Linux ?

So I compiled a list of my requirements, and started researching how I’d be able to fulfill that requirement on Linux.

Without further ado, here’s a complete list of my requirements, what I use on Windows, what I plan on using on Linux, and whether or not I expect difficulties on Linux:

| Requirement | Windows solution | Linux solution | Expecting problems? | Blog post |
|---|---|---|---|---|
| HW accelerated full disk encryption | Windows eDrive | MSED | Yes | [Part 2](http://www.ckode.dk/linux/my-journey-to-linux-part-2/#Encryption) |
| Automatic regular TRIM of SSD | Manually scheduled task | Not certain yet | Yes | [Part 4](http://www.ckode.dk/linux/my-journey-to-linux-part-4/#Trim) |
| Desktop shortcuts similar to Windows | Built-in | No clue | Yes | [Part 4](http://www.ckode.dk/linux/my-journey-to-linux-part-4/#Shortcuts) |
| VPN to Juniper box at work | Juniper Pulse Secure | Openconnect | Yes | [Part 3](http://www.ckode.dk/linux/my-journey-to-linux-part-3/#Juniper) |
| VPN to Private Internet Access | Their Windows client | Network-manager | Yes | Coming soon |
| Encrypted compression of files for cloud storage | 7Zip | p7zip | Yes | Coming soon |
| Automated backup of my blog’s database | Homebrew dot.net application | Homebrew dot.net application using Mono | Yes | Coming soon |
| Using NAS for documents, pictures and so forth | NET USE to mount, then mapping the drive | Editing fstab | No | [Part 3](http://www.ckode.dk/linux/my-journey-to-linux-part-3/#NAS) |
| Browser | Firefox | Firefox | No | Coming soon |
| E-mail | Thunderbird | Thunderbird | No | Coming soon |
| Anti-virus | Bitdefender Internet Security 2016 | Not certain yet | Yes | Coming soon |
| Wireless printer/scanner | Brother’s driver | Not certain yet | Yes | [Part 4](http://www.ckode.dk/linux/my-journey-to-linux-part-4/#Printer) |
| Advanced text editor | Geany | Probably GEdit or Geany | No | Not covered |
| C# development tool | Visual Studio 2015 Community Edition | MonoDevelop | No | Coming soon |
| Cloud based backup storage | Google drive app | Not certain yet, maybe GRive | Yes | Coming soon |
| Password manager | Keepass | Keepass2 | No\* | Not covered |
| Office suite | Libre Office | Libre Office | No | Not covered |
| Music player | MusicBee | Audacious | No | Not covered |
| Mouse driver | Logitech Gaming Software | Not certain anything exists | No\*\* | Not covered |
| Graphics driver | Latest Nvidia driver | Latest proprietary Nvidia driver | No\*\*\* | Not covered |
| Instant Messenger / Voicechat | Skype | Skype | No | Not covered |
| Gaming | Battle.net, Desura, Steam, EA Origin, uPlay, Good Old Games’ Galaxy client | Wine, Desura, Steam, Good Old Games’ gldownloader | Yes | Coming soon |
| Video player | VLC | VLC | No | Not covered |

\* You need to both install Keepass2 and xdotool in order to have Keepass auto paste for you.  
\*\* Without the driver I won’t get warnings about low battery level on my wireless G602, but I can live with that if need be.  
\*\*\* Some Linux distributions don’t have easy access to proprietary drivers, however I’m going to avoid those distributions.

As you can tell I’ve found replacements for most of my needs, and the ones I’m uncertain about is mainly because there’s more than 1 application for the job.

All the lines with a “Yes” in “Expecting problems” will likely be addressed in future blog posts, as I work my way through them.

## Which distribution will I use

Having listed my requirements there is one in particular that weighs heavily in on which distribution to choose: Steam.

Most of my games are on Steam, so I want a distribution with easy installation of Steam, and decent support for it.

SteamOS is out of the question as it’s more of a pure gaming OS, with a normal Ubuntu underneath (it’s derived from Ubuntu).  
I’ve spent a fair amount of time using Ubuntu and Debian in the past, so SteamOS being based on Ubuntu suits me just fine.

I considered a few different Ubuntu derivatives as well as Ubuntu itself, but quickly came to a decision: [Linux Mint](http://www.linuxmint.com/index.php) with Cinnamon desktop manager.  
I don’t like the Unity desktop Ubuntu uses now a days, and Xubuntu seems a tad bit too simple, even though I prefer simple desktops, so the Gnome 3 inspired Cinnamon hit the spot for me.  
Furthermore Linux Mint has a large user base and community, which will probably come in handy down the road.

That’s all for this blog post, I’m off to installing Mint now – the next post will follow up on the installation of probably some of the elements of the above list 🙂