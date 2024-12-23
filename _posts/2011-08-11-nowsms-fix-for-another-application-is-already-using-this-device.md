---
title: 'NowSMS - Fix for "Another application is already using this device"'
layout: post
---

We’ve been using [NowSMS Lite](http://www.nowsms.com/) for a while at work. It’s the SMS Gateway for our monitoring system (it monitors all servers, services, etc.)

Recently we moved the monitor server, and after starting it up again, NowSMS wasn’t working.

It said communication failure under service, and when trying to test the modem, we received this error:  
**Unable to access modem at COMx: — Error 5 — Access Denied — Another application is already using this device**

Quite peculiar, as all we did was shutdown and move the server.

The fix however, is even more weird…

I started by updating the NowSMS software to its newest version. Their updates can be found here: <http://www.nowsms.com/category/updates>.

Afterwards I again tried to test the modem – same error message.

Then I noticed something new in the newest version: I could choose either the “standard 33600 modem” driver or the actual COM port the modem is connected to – In my case COM2.

Using COM2 everything suddenly just started working.

Why you have to install a standard 33600 driver (or the particular driver for your modem, should you have a driver for it) only to \*NOT\* use it beats me.