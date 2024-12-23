---
title: 'Samsung Tizen disconnects or freeze when playing DLNA content: How to fix'
layout: post
---

I’ve recently acquired a Samsung Tizen based TV, and much to my dismay it has this very annoying bug, where it’ll either disconnect from the DLNA source, or just freeze the video, when playing anything over DLNA.  
This was never a problem with my previous Samsung TV, which was the last generation before Tizen.

I’m using a Synology NAS as DLNA server, but the issue applies to using a PC, Playstation, etc. as well, according to tons of forum questions about this.

Despite the many forum questions, many of them directly on Samsung’s own forum, none of them really end up with a solution.

So I tried quite a few things myself. I’ve used Synology Media Server, Videostation and Plex on the Synology side.  
Likewise I tried using the TV’s built-in player (simply selecting the DLNA server as source, and just browsing the content), I tried installing the Videostation app, and finally Plex.

The built-in player would stop playing the video at some point and show a message saying it disconnected. Videostation would just freeze the playback, and Plex I can’t really remember, but I’m pretty sure it stopped playing as well.

Now on to the FIX:

I went back to using Synology Media Server, which just advertises its content, and then using the built-in player on the TV.

I started noticing a pattern in the disconnects: They’d all occur around 15 minutes into the video. This got me thinking there might be some sort of timeout or similar on the Synology server, that the TV wouldn’t handle properly.

So I checked all the settings for Synology Media Server, and spotted the “SSDP Advertisement Interval” which was set at 920 seconds by default. This value matches the roughly 15 minutes I’ve noticed went by before disconnecting.

The setting controls how often the Synology will broadcast a message on the network saying “Hi I’m a DLNA server”.

I therefore tried increasing this value to 9200 seconds (simply adding a zero :-D) – giving me 2,5 hour between the advertisements. **And this has so far completely solved the problem!**

And let me just be clear: This IS a bug in the Tizen TV. SSDP advertisement is NOT supposed to break the current content playback.

I’ve had no side effects of the higher interval, as the TV seems to remember the DLNA server after having been connected once.

If you’ve got a lot of different devices entering and leaving your network, you may have a different experience, as they may not pick up on your DLNA server as easily due to the long intervals between advertisements.

So in conclusion: If your DLNA server let’s you control the advertisement interval, give it a try, it just might fix this insanely annoying bug.

Leave me a comment if this helps you, or if you have any questions 🙂