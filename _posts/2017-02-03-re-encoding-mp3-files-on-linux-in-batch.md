---
id: 318
title: 'Re-encoding mp3 files on linux in batch'
date: '2017-02-03T23:47:12+01:00'
author: Steffen
excerpt: "I recently needed to re-encode a lot of my ripped CDs to a lower bit rate for my car stereo (it doesn't cope well with 320 kbit apparently)\r\n\r\nSo I figured it'd be easy to do in a bash shell, and it was :-)"
layout: post
guid: 'http://www.ckode.dk/?p=318'
permalink: /linux/re-encoding-mp3-files-on-linux-in-batch/
categories:
    - Linux
    - Script
---

I recently needed to re-encode a lot of my ripped CDs to a lower bit rate for my car stereo (it doesn’t cope well with 320 kbit apparently)

So I figured it’d be easy to do in a bash shell, and it was. I opted for using parallel to speed things up, by using all my CPU cores at once.

Start out by installing the necessary tools:

`sudo apt install lame parallel`

Then either create a bash script, or just copy-paste the script. I saved it as a bash script for easy re-runs in the future:

`#!/bin/bash<br></br>find . -name "*.mp3" | parallel lame --mp3input -m s -h -b 192 --cbr {0} {0}new<br></br>find . -name "*.mp3" -exec rm '{}' \;<br></br>find . -name "*.mp3new" -exec rename 's/mp3new/mp3/;' '{}' \;<br></br>`

It works by first finding all your mp3 files in the current working directory and feeding them through parallel to lame. I’ve gone with a constant bit rate (cbr) of 192 kbit.  
Then I delete all the old files, and finally I rename all the “mp3new” to simply “mp3” – voila job’s done.

Hope this little script is of use to you 🙂