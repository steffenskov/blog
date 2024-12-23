---
id: 240
title: 'Media file indexing broken in Synology DSM 5.0'
date: '2014-03-22T19:34:15+01:00'
author: Steffen
excerpt: "Having just updated my Synology DS213j to DSM 5.0 I discovered that new media files were no longer being indexed.\r\n\r\nThis in turn meant my DLNA was worthless, since new content would never index and thus never show.\r\n\r\nApparently this issue only occurs when you have encrypted at least one shared folder (even though it's not used for media files, and not configured to be indexed)\r\n\r\nSynology has yet to come up with a proper fix for this, so until they do you can fix it like this:"
layout: post
guid: 'http://www.ckode.dk/?p=240'
permalink: /server-configuration/media-file-indexing-broken-in-synology-dsm-5-0/
categories:
    - 'Server configuration'
tags:
    - Synology
---

Having just updated my Synology DS213j to DSM 5.0 I discovered that new media files were no longer being indexed.

This in turn meant my DLNA was worthless, since new content would never index and thus never show.

Apparently this issue only occurs when you have encrypted at least one shared folder (even though it’s not used for media files, and not configured to be indexed)

Synology has yet to come up with a proper fix for this, so until they do you can fix it like this:

1. Open a browser and go to your Synology’s web based administration.
2. Go to control panel -&gt; Terminal and SNMP
3. Enable SSH and apply the setting
4. Open an SSH terminal to the synology, in Windows you can do this using the program Putty, which is free and can be downloaded here: <http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html> (You only need putty.exe)
5. Type your Synology’s IP into “Host name” in Putty and click the Open button.
6. This opens a black terminal window which asks a user name – I believe you have to use “root” for this, but feel free to try with “admin” if you cannot log in as root.
7. It then asks for a password, this should be the same one you use to log into the web based administration.
8. Once in, type the following lines – each line must be followed by pressing enter:  
    cd /var/spool  
    rm syno\_indexing\_queue\*  
    killall synoindexd  
    killall synoindexscand  
    exit
9. It should now have closed down the terminal window, go back to your Synology’s web based administration, and go to control panel -&gt; Media library
10. Here there is a button for reindexing your content – press it and you’re golden.
11. NOTE: Once it starts reindexing it may take quite a while, depending on how much content you have.
12. NOTE: If you restart the Synology you will need to go through these steps again, as it’ll break again upon restarting.