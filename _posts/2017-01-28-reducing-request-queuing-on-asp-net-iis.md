---
title: 'Reducing request queuing on ASP.Net / IIS'
layout: post
---

If you’re hosting an ASP.Net website on Microsoft IIS, you’ve likely seen requests being queued from time to time.

As a visitor to a website, you can easily tell you’ve been queued, if the “spinning loader” in your browser keeps rotating counter-clockwise (at least this is the case for Firefox which is my main browser). The second it changes to rotating clockwise, it’s started to receive data from the server.

This can happen for a lot of different reasons, a common cause is your servers CPU being overburdened. However it can also occur when you have plenty of CPU headroom to spare. I recently encountered this myself, and found a nice simple solution for it.

The reason this happens even when you have plenty of CPU power available, is the IIS keeps shutting down its threads. When a new request comes in it has to spin up a new thread, which takes time. While waiting for the new thread, the request is queued.

Luckily there’s a configuration for this parameter. I’ll start off by showing the config I ended up using:

`<system.web>
<httpRuntime minFreeThreads="8" minLocalRequestFreeThreads="8" />
<processModel autoConfig="false" minWorkerThreads="75" maxWorkerThreads="150" maxIoThreads="150" />
</system.web>`

This config goes into the machine.config file, which can be found at

32 bit: `%windir%\Microsoft.NET\Framework\[version]\config\machine.config`  
64 bit: `%windir%\Microsoft.NET\Framework64\[version]\config\machine.config`

Normally you’ll already have at least &lt;httpRuntime /&gt; and probably also &lt;processModel /&gt; elements, so you’ll have to find them and alter them rather than simply adding my config to the end of the file (as this would break the IIS)

Here’s what the config does:

- **minFreeThreads**: Determines how many threads MUST be free at all time, this ensures the server has a thread available when a request comes in (unless 9+ requests come in at the same time)
- **minLocalRequestFreeThreads**: Essentially the same as the previous parameter, only for local requests this time. Normally you won’t have many local requests, but for good measure I configure them the same way.
- **autoConfig**: Defaults to “true” and must be set to “false” in order for the rest of the config to have any effect.
- **minWorkerThreads**: Defaults to 1! Determines how many threads the IIS should always keep running. Note that this value is multiplied by number of CPU cores. So for a quad core server my value of 75 would yield 400 threads. (75 may be way to high for your setup, the site I did this for is processing up to 1600 requests / s with a single quad core server)
- **maxWorkerThreads**: Same as above, only this one is a maximum. Microsoft suggests keeping maximum at twice the value of minimum. This one too is multiplied by CPU core count.
- **maxIoThreads**: Same as above except used for I/O threads (reading from Disk, pushing data over the network etc.)

Apart from this, there’s also another section of machine.config that you may want to address as well:

`<system.net>
<connectionManagement>
<add address="*" maxconnection="48" />
</connectionManagement>
</system.net>`

Normally there won’t be a &lt;system.net&gt; section already, but search just to be sure before adding it.

This instructs the IIS to service no more than 48 connections simultaneously. This value is actually another Microsoft recommendation – they recommend setting it at 12\*CPU cores count. The value is not automatically multiplied, so if you change your servers CPU core count, you need to remember updating this one as well.

I’ve followed Microsofts guideline on this and haven’t seen any requests getting queued at all after implementing these changes.

NOTICE: You will probably have to restart the server for every setting to take effect – I only noticed a partly effect before restarting.

You can read more about the subject at Microsoft here: [Contention, poor performance, and deadlocks when you make calls to Web services from an ASP.NET application](https://support.microsoft.com/en-us/help/821268/contention,-poor-performance,-and-deadlocks-when-you-make-calls-to-web-services-from-an-asp.net-application)  
Even though Microsoft’s article is from IIS 6 and ASP.Net 2.0 it’s still correct today, as IIS 10 / ASP.Net 4.7 on Windows Server 2016 still responds correctly to the registry keys.