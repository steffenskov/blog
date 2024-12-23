---
id: 254
title: 'Fixing League of Legends crashes'
date: '2014-10-25T13:10:38+01:00'
author: Steffen
excerpt: "If you're playing League of Legends there's a good chance you have run into one or more stability issues.\r\n\r\nHere I'll cover the one where the game crashes immediately after champion select (you won't even get to see the loading screen with the champions)"
layout: post
guid: 'http://www.ckode.dk/?p=254'
permalink: /gaming/fixing-league-of-legends-crashes/
categories:
    - Gaming
tags:
    - 'League of Legends'
---

If you’re playing League of Legends there’s a good chance you have run into one or more stability issues.

Here I’ll cover the one where the game crashes immediately after champion select (you won’t even get to see the loading screen with the champions)

This is caused by Logitech Gaming Software, yup you read that right.

If you’re using any Logitech gaming gear (usually named Gxxx, e.g. G602 mouse, G930 headset, and so forth) you’re likely running Logitech Gaming Software as well.

The cause is in fact two Logitech dll files which League of Legends use for god knows what.

And the fact is very simple: delete the files. Regardless of whether you run Logitech Gaming Software or not, these files are safe to delete – the game won’t miss them, and will in fact stop crashing to boot.

The files can found in your League of Legends installation directory, which is likely somewhat similar to this:

`C:\Program Files\League of Legends\RADS\solutions\lol_game_client_sln\releases\0.0.1.62\deploy`

Do note that the first part of the path “C:\\Program Files\\League of Legends” as well as the the version number “0.0.1.62” may vary.

Inside the deploy folder are two files: `LogitechGkey.dll` and `LogitechLed.dll`  
You can either delete them, rename them or move them to a separate folder. Personally I created a folder named “Logitech” and moved them in there – this way I can restore them if need be.

Feel free to leave a comment if this doesn’t work you 🙂

Also this fix was first discovered on reddit, so if you’re curious you can read more about it here: [http://www.reddit.com/r/leagueoflegends/comments/1gn3vt/fix\_crash\_after\_champion\_select/](http://www.reddit.com/r/leagueoflegends/comments/1gn3vt/fix_crash_after_champion_select/)