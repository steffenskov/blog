---
id: 305
title: 'XMP &#8211; Do you have free performance just waiting for you?'
date: '2016-04-04T14:56:40+01:00'
author: Steffen
excerpt: "Pretty much everyone I know never enable XMP, so I figure a blog post about what, why and how was in order.\r\n\r\nHere I'll explain what XMP is, why you want to enable it, and how to do it. Most likely you'll get some free performance by following this post."
layout: post
guid: 'http://www.ckode.dk/?p=305'
permalink: /desktop-machines/xmp-do-you-have-free-performance-just-waiting-for-you/
categories:
    - 'Desktop machines'
---

Pretty much everyone I know never enable XMP, so I figure a blog post about what, why and how was in order.

Here I’ll explain what XMP is, why you want to enable it, and how to do it. Most likely you’ll get some free performance by following this post.

**What is XMP?**  
XMP is an acronym and stands for eXtreme Memory Profile. As you can probably tell it’s about RAM in your PC.

When you buy for instance 1600mhz DDR 3 RAM rated at C9 timings, these RAM will be shipped with an XMP (it’s a profile, remember) configuring them to run at 1600mhz with C9 timings.  
But they’re also shipped with a DEFAULT profile configuring them at 1333mhz with C10, C11 or even worse timings. (For timings, less is faster – as the timings are how long delays the RAM uses between doing something)

**Why enable XMP?**  
Before you enable XMP your RAM will run at their default profile, and therefore be running quite a bit slower than they should. In fact if you don’t enable XMP you’ve probably paid too much for your RAM.

At this point I should mention that all (or at least all I’ve seen) DDR 3 RAM ship with a default profile of 1333mhz. So if you buy 2400mhz DDR3, they’ll still only run 1333mhz until you enable XMP.

**How to enable XMP?**  
This is done by entering your motherboards UEFI or BIOS (modern motherboards use UEFI, and older ones use BIOS – to keep things simple I’ll just use the word UEFI throughout the rest of the post)

In here you want to look for either the acronym “XMP” or “Memory profile” or “Memory setting”. It depends on the UEFI what the setting is called, but it’s always there somewhere.  
In case your motherboard doesn’t just have an “Enabled/Disabled” switch, but instead wants you to select a specific profile, you usually want “Profile #1” (the first one).

After enabling it, you want to save settings and reboot the PC. After reboot I always like to go back into UEFI and check the memory speed is now correctly listed. Usually the UEFI has a status page as its first page, which will include this information.

**Why is all this necessary?**  
As an addendum I figured I’d briefly explain why RAM is built like this. The reason is somewhat simple: The RAM manufacturers blame the motherboard manufacturers for lacking standards and vice versa.

So the way the world currently works, is the RAM manufacturers don’t know for sure what motherboard you’ll insert the RAM into. If you were to stuff 2400mhz DDR3 into a low end motherboard that doesn’t support that speed, it’d refuse to boot if the RAM defaulted to 2400mhz.

But all motherboards with DDR3 support, can handle 1333mhz, so this has become the default speed for all DDR3 RAM. Once you’ve put in the memory, you can switch on XMP when you know your motherboard can handle it. (And most consumer boards can, server boards are typically a bit more limited in what they support)

So the current situation requires you to handle two things:

1. Figure out how fast RAM your motherboard/CPU can handle
2. Enable that speed using XMP

Hope this blog post has helped you gain that “free” performance (technically it’s not free, as you paid for that RAM – but if you didn’t know about XMP, it’s a free upgrade to your current situation)

Feel free to leave a comment if you have questions, or if I simply overlooked something 🙂