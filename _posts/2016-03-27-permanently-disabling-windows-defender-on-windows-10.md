---
id: 302
title: 'Permanently disabling Windows Defender on Windows 10'
date: '2016-03-27T20:55:30+01:00'
author: Steffen
excerpt: "Until recently Windows Defender would automatically disable, if you ran a purchased Antivirus (or one of the many free ones)\r\n\r\nHowever this is not the case anymore, or at least it hasn't been for me. \r\n\r\nHere's how to permanently disable Windows Defender, so you can rely on your purchased Antivirus alone."
layout: post
guid: 'http://www.ckode.dk/?p=302'
permalink: /desktop-machines/permanently-disabling-windows-defender-on-windows-10/
categories:
    - 'Desktop machines'
tags:
    - 'Windows 10'
---

Until recently Windows Defender would automatically disable, if you ran a purchased Antivirus (or one of the many free ones)

However this is not the case anymore, or at least it hasn’t been for me. All of a sudden I’d have both NOD32 and Windows Defender running at the same time.

Since I know double antivirus isn’t equal to double the protection, and since I know NOD32 handles most tests much better than Windows Defender, I’d like to disable Windows Defender entirely.

In Windows 10 this is no longer an easy option – because Windows will automatically re-enable Windows Defender after having disabled it for a while.

However if you’re using Windows 10 Pro, there’s another option: Group Policies. There’s luckily a Group Policy for entirely disabling Windows Defender, and it’s aptly named “Turn off Windows Defender”.

There are no side effects of this, and no automatic re-enabling. So here’s how to do it:

1. Hold the Windows key on your keyboard and press R, this will open the **Run** window.
2. Enter **gpedit.msc** and press OK, this will open the **Group Policy** window. (You’ll probably be asked whether you’re sure you want to open it by the UAC system)
3. Navigate to **Local Computer Policy** -&gt; **Computer Configuration** -&gt; **Administrative Templates** -&gt; **Windows Components** -&gt; **Windows Defender**.
4. In here you’ll find **Turn off Windows Defender**, double click it to edit it.
5. Set it to **Enabled** instead of **Not configured** and press OK. This will immediately shutdown Windows Defender, and prevent it from running ever again.

Hope this helps you guys – it sure helped me 🙂