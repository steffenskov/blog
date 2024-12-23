---
id: 169
title: 'Lost intellisense for custom controls in Visual Studio'
date: '2011-05-21T06:23:27+01:00'
author: Steffen
excerpt: "Visual Studio can lose intellisense support in HTML view for ASP.Net sites.\r\n\r\nHere's how to recover it."
layout: post
guid: 'http://www.ckode.dk/?p=169'
permalink: /programming/lost-intellisense-for-custom-controls-in-visual-studio/
categories:
    - Programming
tags:
    - ASP.Net
    - 'Visual Studio'
---

I had a quite peculiar experience, after reinstalling Windows on my work PC.

For some odd reason, I had \*no\* intellisense for our custom controls, when working in ASPX files (the HTML view)

VS simply claimed the prefix didn’t exist, and thus no intellisense.

After googling about a bit, I found the solution: VS generates XML schemas (XSD files) for your controls, and uses these for the intellisense. If this goes wrong, you can simply wipe the generated schemas, and it’ll regenerate them.

The schemas can be found in “%APPDATA%\\Microsoft\\VisualStudio\\10.0\\ReflectedSchemas” (Replace 10.0 with 9.0 if you’re still running VS 2008)

And for those who don’t know %APPDATA%, it’s a shortcut to the folder used for storing user specific application settings. Just copy-paste the above path into a the “run” window in Windows, and it’ll open.

Before doing anything though, you must first close all instances of Visual Studio.

Once that’s done, delete all files in the folder.

Start VS again, and if it still hasn’t got any intellisense, close it and start it once more – then it should generate the files.

I know it sounds odd, that you may have to close it twice, and I have no explanation of why – it’s just how it worked out for me.